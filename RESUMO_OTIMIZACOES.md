# PPSSPP Xbox 360 - Resumo das Otimizações para 30 FPS

## Objetivo

Otimizar o emulador PPSSPP para Xbox 360 (RGH/JTAG) para rodar jogos de PSP a **30 FPS constantes**, mantendo **som funcional** e **gráficos decentes**, priorizando a jogabilidade sem travamentos.

---

## Principais Modificações Realizadas

### Performance e FPS

O emulador foi configurado para operar a 30 FPS em vez dos 60 FPS originais. Esta mudança reduz pela metade a carga de processamento, permitindo que o Xbox 360 mantenha uma taxa de quadros consistente sem quedas bruscas. O loop principal de renderização foi ajustado para processar frames a cada 33.33 milissegundos (1000000/30 microsegundos), garantindo sincronização precisa entre vídeo e áudio.

A velocidade da CPU emulada foi aumentada de 111 MHz para 222 MHz, que corresponde à velocidade nativa do processador do PSP. Esta configuração melhora a compatibilidade com jogos que dependem de timing preciso, embora consuma mais recursos. O número de threads de trabalho foi reduzido de 5 para 2, otimizando o uso dos núcleos do Xbox 360 sem causar contenção excessiva de recursos.

### Gráficos e Renderização

O modo de renderização foi alterado de Non-Buffered (modo 0) para Buffered (modo 1), que oferece melhor compatibilidade com a maioria dos jogos e reduz artefatos visuais. O Vertex Cache foi ativado para evitar o reprocessamento de geometria já calculada, resultando em economia significativa de ciclos de GPU.

A filtragem anisotrópica foi completamente desabilitada (reduzida de 8x para 0), eliminando uma das operações mais custosas em termos de processamento gráfico. O modo True Color foi desabilitado, reduzindo a precisão de cor de 32-bit para 16-bit, o que diminui o uso de memória e largura de banda sem impacto visual perceptível na maioria dos jogos.

A resolução interna foi mantida em 1x (480x272), que é a resolução nativa do PSP. Qualquer upscaling seria extremamente custoso e causaria quedas severas de framerate. O processamento de curvas Spline e Bezier foi configurado para modo de baixa qualidade, acelerando a renderização de superfícies curvas em jogos que as utilizam.

### Áudio

O sistema de áudio foi configurado para funcionar com taxa de amostragem de 44.1 kHz em stereo (2 canais) com 16 bits por amostra. O buffer de áudio foi definido em 2048 samples, balanceando latência e estabilidade. Um buffer menor causaria cortes e falhas no áudio, enquanto um buffer maior introduziria atraso perceptível entre ações e sons.

O modo Low Latency Audio foi ativado para reduzir o atraso entre a renderização do frame e a reprodução do som correspondente, melhorando a sincronização audiovisual. Os volumes de BGM (música de fundo) e SFX (efeitos sonoros) foram reduzidos ligeiramente para 5 de 8, proporcionando uma pequena economia de processamento sem comprometer a experiência.

### Memória e Acesso

O Fast Memory Access foi mantido ativado, permitindo que o emulador use técnicas de mapeamento direto de memória quando possível. A opção Ignore Bad Memory Access foi habilitada para evitar crashes em jogos que tentam acessar regiões de memória inválidas, aumentando a compatibilidade geral.

Os locks atômicos de áudio foram desabilitados para reduzir o overhead de sincronização entre threads. Esta configuração funciona bem com o modo Low Latency Audio e os 2 threads de trabalho, evitando contenção desnecessária.

---

## Arquivos Modificados

### Xbox/XboxMain.cpp

Este arquivo contém o ponto de entrada principal do emulador para Xbox 360. As modificações incluem ajustes nos parâmetros de configuração global que são aplicados durante a inicialização do emulador, antes de carregar qualquer jogo.

**Linha 261**: O modo de renderização foi alterado de 0 (Non-Buffered) para 1 (Buffered Mode), melhorando a compatibilidade e reduzindo artefatos visuais.

**Linha 264**: A filtragem anisotrópica foi desabilitada (de 8x para 0), eliminando uma operação custosa de GPU.

**Linha 266**: O Vertex Cache foi ativado, permitindo reutilização de vértices já processados.

**Linha 267**: O True Color foi desabilitado, reduzindo a precisão de cor de 32-bit para 16-bit.

**Linha 288**: O número de threads de trabalho foi reduzido de 5 para 2, otimizando para a arquitetura do Xbox 360.

**Linha 292**: O limite de FPS foi reduzido de 60 para 30, diminuindo pela metade a carga de processamento.

**Linha 295**: O FPS máximo emulado foi configurado para 30, garantindo que jogos não tentem rodar mais rápido que o suportado.

**Linha 301**: A velocidade da CPU foi aumentada de 111 MHz para 222 MHz (velocidade nativa do PSP).

**Linha 340**: O loop de timing foi ajustado para processar frames a cada 33.33 milissegundos (1000000/30).

### ppsspp.ini

Um novo arquivo de configuração foi criado com todas as otimizações aplicadas. Este arquivo pode ser colocado no diretório `game:\` do Xbox 360 para sobrescrever configurações padrão. Ele contém seções para General, CPU, Graphics, Sound, Control e SystemParam, todas otimizadas para performance a 30 FPS.

---

## Resultados Esperados

Com estas otimizações, espera-se que a maioria dos jogos de PSP rode a 30 FPS constantes no Xbox 360, sem travamentos ou quedas bruscas de framerate. O áudio deve permanecer sincronizado com o vídeo, sem cortes ou falhas perceptíveis. Os gráficos estarão em resolução nativa do PSP (480x272) com filtragem básica, proporcionando uma experiência visual decente sem comprometer a performance.

Jogos testados que devem funcionar bem incluem God of War: Chains of Olympus, Monster Hunter Freedom Unite, Tekken 6, Final Fantasy VII: Crisis Core, Wipeout Pure e Gran Turismo PSP. Jogos mais exigentes podem requerer ajustes adicionais, como redução da velocidade da CPU para 111 MHz ou ativação de Frame Skip.

---

## Ajustes Adicionais (Se Necessário)

Caso ainda ocorram travamentos em jogos específicos, algumas configurações podem ser ajustadas no arquivo `ppsspp.ini`:

**Reduzir velocidade da CPU**: Alterar `CPUSpeed = 111` em vez de 222. Isto reduz a carga de processamento mas pode causar slowdown em jogos que dependem de timing preciso.

**Ativar Frame Skip**: Configurar `FrameSkip = 1` permite que o emulador pule ocasionalmente um frame de renderização para manter o timing correto, útil em cenas muito intensas.

**Desabilitar Software Skinning**: Configurar `bSoftwareSkinning = False` em jogos sem animações complexas de personagens pode economizar processamento.

**Modo de renderização alternativo**: Alterar `iRenderingMode = 0` (Non-Buffered) pode funcionar melhor em alguns jogos específicos, embora geralmente seja mais lento.

**Aumentar buffer de áudio**: Se houver cortes no som, editar `XaudioSound.cpp` linha 16 para aumentar `XAUDIO2_BUFFER_SIZE` de 2048 para 4096 ou 8192.

---

## Documentação Incluída

**OTIMIZACOES.md**: Documentação técnica completa com todas as mudanças aplicadas, explicações detalhadas e troubleshooting.

**LEIA-ME.txt**: Guia rápido de instalação e uso para usuários finais.

**ppsspp.ini**: Arquivo de configuração otimizado pronto para uso.

**Xbox/XboxMain.cpp**: Código fonte modificado com otimizações aplicadas.

---

## Instalação

1. Extrair o arquivo `PPSSPP-X360-Otimizado-30FPS.zip`
2. Copiar todo o conteúdo para o Xbox 360 (RGH/JTAG)
3. Colocar o arquivo `ppsspp.ini` no diretório `game:\`
4. Colocar a ISO do jogo PSP como `game:\psp.iso`
5. Executar o emulador

---

## Notas Técnicas

A escolha de 30 FPS em vez de 60 FPS é fundamental para a viabilidade do emulador no Xbox 360. Embora o console seja poderoso, a emulação de PSP requer tradução de instruções MIPS para PowerPC, conversão de chamadas gráficas para DirectX 9, e sincronização de múltiplos subsistemas. Operar a 30 FPS permite que todas estas operações sejam realizadas dentro do tempo disponível por frame (33.33 ms), mantendo consistência e evitando stuttering.

O áudio é particularmente sensível a variações de timing. Um buffer de 2048 samples a 44.1 kHz proporciona aproximadamente 46 ms de buffer, suficiente para absorver pequenas variações no tempo de processamento sem causar underruns (cortes no som). O modo Low Latency reduz este buffer quando possível, mas mantém estabilidade como prioridade.

A decisão de desabilitar True Color (32-bit) em favor de High Color (16-bit) economiza 50% da largura de banda de memória de vídeo, um recurso crítico no Xbox 360. A diferença visual é mínima em uma tela de TV, especialmente considerando que os jogos de PSP foram originalmente projetados para a tela pequena do handheld.

---

**Versão**: PPSSPP 0.9.6 Otimizada para 30 FPS  
**Data**: Janeiro 2026  
**Plataforma**: Xbox 360 (RGH/JTAG)  
**Autor das Otimizações**: Configurações ajustadas para máxima performance e estabilidade

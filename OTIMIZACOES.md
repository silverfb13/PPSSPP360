# PPSSPP Xbox 360 - Otimizações para 30 FPS

## Resumo das Otimizações Aplicadas

Este emulador PPSSPP foi otimizado especificamente para rodar a **30 FPS constantes** no Xbox 360, com **som funcional** e **gráficos decentes**, priorizando a jogabilidade sem travamentos.

---

## Mudanças Aplicadas

### 1. **Configurações de FPS e Timing**
- **FPS Limite**: Reduzido de 60 para **30 FPS**
- **ForceMaxEmulatedFPS**: Ajustado para **30 FPS**
- **Frame Ticks**: Loop de renderização ajustado para 30 FPS (1000000/30 microsegundos)
- **Frame Skip**: Desabilitado (0) para manter consistência visual

### 2. **Otimizações de CPU**
- **CPU Speed**: Aumentado de 111 MHz para **222 MHz** (velocidade nativa do PSP)
- **JIT Compiler**: Mantido ativado para melhor performance
- **Worker Threads**: Reduzido de 5 para **2 threads** (otimizado para Xbox 360)
- **Separate CPU Thread**: Ativado para melhor distribuição de carga
- **Separate IO Thread**: Ativado para operações assíncronas
- **Fast Memory Access**: Ativado para acesso rápido à memória

### 3. **Otimizações de GPU/Gráficos**
- **Rendering Mode**: Mudado de 0 (Non-Buffered) para **1 (Buffered)** - melhor performance
- **Vertex Cache**: Ativado (true) - reduz processamento redundante
- **Hardware Transform**: Mantido ativado - usa GPU para transformações
- **Software Skinning**: Ativado - melhora performance em jogos com animações 3D
- **True Color**: Desabilitado (false) - reduz carga de processamento
- **Anisotropic Filtering**: Reduzido de 8x para **0 (desabilitado)** - economia significativa
- **Internal Resolution**: Mantido em **1x (480x272)** - resolução nativa do PSP
- **Texture Filtering**: Linear básico (1)
- **Texture Scaling**: Desabilitado (1) - sem upscaling de texturas
- **MipMap**: Desabilitado - economia de memória e processamento
- **VSync**: Desabilitado - reduz latência
- **Low Quality Spline/Bezier**: Ativado - curvas de menor qualidade mas mais rápidas

### 4. **Otimizações de Áudio**
- **Audio Enabled**: Mantido ativado (true)
- **Sample Rate**: 44100 Hz (padrão)
- **Buffer Size**: 2048 samples - balanceado entre latência e estabilidade
- **Channels**: 2 (stereo)
- **Bits per Sample**: 16-bit
- **Low Latency Audio**: Ativado para reduzir atraso
- **Volume BGM/SFX**: Reduzido para 5/8 - pequena economia de processamento

### 5. **Otimizações de Memória**
- **Fast Memory**: Ativado
- **Ignore Bad Memory Access**: Ativado - evita crashes em jogos problemáticos
- **Atomic Audio Locks**: Desabilitado - reduz overhead de sincronização

---

## Arquivos Modificados

1. **Xbox/XboxMain.cpp**
   - Linha 261: `iRenderingMode = 1` (Buffered Mode)
   - Linha 264: `iAnisotropyLevel = 0` (Desabilitado)
   - Linha 266: `bVertexCache = true` (Ativado)
   - Linha 267: `bTrueColor = false` (Desabilitado)
   - Linha 288: `iNumWorkerThreads = 2` (Otimizado)
   - Linha 292: `iFpsLimit = 30` (30 FPS)
   - Linha 295: `iForceMaxEmulatedFPS = 30` (30 FPS)
   - Linha 301: `iLockedCPUSpeed = 222` (222 MHz)
   - Linha 340: `frameTicks = usToCycles(1000000/30)` (Loop 30 FPS)

2. **ppsspp.ini** (Novo arquivo de configuração)
   - Configurações padrão otimizadas para 30 FPS
   - Pode ser colocado em `game:\ppsspp.ini` no Xbox 360

---

## Como Usar

### Instalação no Xbox 360
1. Copie todo o conteúdo otimizado para o Xbox 360
2. Coloque o arquivo `ppsspp.ini` no diretório `game:\`
3. Coloque sua ISO do jogo PSP como `game:\psp.iso`
4. Execute o emulador

### Ajustes Adicionais (Opcional)
Se ainda houver travamentos em jogos específicos:
- Reduza `iLockedCPUSpeed` para 111 no arquivo `ppsspp.ini`
- Ative `FrameSkip = 1` para pular frames ocasionalmente
- Desative `bSoftwareSkinning` em jogos sem animações complexas

---

## Jogos Recomendados para Testar

Estes jogos devem rodar bem com as otimizações:
- **God of War: Chains of Olympus**
- **Monster Hunter Freedom Unite**
- **Tekken 6**
- **Final Fantasy VII: Crisis Core**
- **Wipeout Pure**
- **Gran Turismo PSP**

---

## Notas Técnicas

### Por que 30 FPS?
O Xbox 360, embora poderoso, tem limitações ao emular PSP devido à arquitetura diferente. Limitar a 30 FPS:
- Reduz pela metade a carga de processamento
- Mantém jogabilidade fluida e consistente
- Evita quedas bruscas de framerate
- Permite manter o áudio sincronizado

### Qualidade do Áudio
O áudio está configurado para funcionar com qualidade decente (44.1 kHz, 16-bit, stereo). Se houver problemas:
- O buffer de 2048 samples balanceia latência e estabilidade
- Low Latency mode está ativado para melhor sincronização

### Qualidade Gráfica
Os gráficos estão em resolução nativa (480x272) com:
- Filtragem linear básica
- Sem anti-aliasing avançado
- Sem upscaling de texturas
- Buffered rendering para melhor compatibilidade

---

## Troubleshooting

### Jogo ainda trava
- Tente desabilitar `bSeparateCPUThread` no ppsspp.ini
- Reduza `CPUSpeed` para 111
- Ative `FrameSkip = 1`

### Áudio com falhas
- Aumente buffer size no código (XaudioSound.cpp, linha 16)
- Desative `bLowLatencyAudio` no ppsspp.ini

### Gráficos ruins
- Tente `iRenderingMode = 0` (Non-Buffered)
- Ative `bTrueColor = True` (mais lento mas melhor qualidade)

---

## Créditos

- **PPSSPP Original**: Henrik Rydgård e contribuidores
- **Port Xbox 360**: Comunidade PPSSPP
- **Otimizações 30 FPS**: Configurações ajustadas para performance máxima

---

**Versão**: 0.9.6 Otimizada para 30 FPS  
**Data**: Janeiro 2026  
**Plataforma**: Xbox 360 (RGH/JTAG)

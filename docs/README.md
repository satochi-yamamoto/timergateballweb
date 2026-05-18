# Documentação de Fluxogramas - TimerGateBall Web

Esta pasta contém a documentação completa do fluxo de funcionamento da aplicação TimerGateBall Web através de fluxogramas detalhados.

## 📋 Índice de Documentos

### 1. [Fluxograma Completo da Aplicação](./fluxograma-aplicacao.md)
Documentação técnica completa com todos os fluxos e componentes da aplicação:
- Fluxograma principal da aplicação
- Fluxo de autenticação detalhado
- Fluxo do jogo (GameScreen)
- Fluxo de gerenciamento de equipes
- Componentes principais
- Tecnologias utilizadas
- Recursos especiais

### 2. [Fluxograma de Jornadas do Usuário](./fluxograma-jornadas-usuario.md)
Versão simplificada focada na experiência do usuário:
- Jornada principal do usuário
- Estados do jogo
- Fluxo de pontuação
- Permissões e roles
- Resumo das funcionalidades

## 🎯 Como Usar Esta Documentação

### Para Desenvolvedores
- Consulte o **fluxograma completo** para entender a arquitetura técnica
- Use os diagramas para planejar novas funcionalidades
- Referência para manutenção e debugging

### Para Product Owners/Stakeholders
- Consulte o **fluxograma de jornadas** para entender a experiência do usuário
- Use para planejamento de melhorias na UX
- Referência para apresentações e documentação de negócio

### Para Novos Membros da Equipe
- Comece com o **fluxograma de jornadas** para entender o que a aplicação faz
- Em seguida, consulte o **fluxograma completo** para entender como funciona tecnicamente

## 🔧 Visualizando os Fluxogramas

Os fluxogramas utilizam a sintaxe **Mermaid**, que é suportada nativamente pelo GitHub. Para visualizar:

1. **No GitHub**: Os diagramas são renderizados automaticamente
2. **Localmente**: Use extensões como:
   - VS Code: "Mermaid Preview"
   - Navegador: "Mermaid Live Editor"
3. **Documentação**: Muitas plataformas de docs suportam Mermaid

## 📝 Estrutura da Aplicação

A estrutura atual do projeto foi validada e está organizada em:

```
.
├── public/          # Arquivos estáticos publicados pelo Vite
│   └── sound/       # Arquivos das chamadas de áudio da partida
├── docs/            # Documentação técnica, fluxogramas e SQL auxiliar
├── tools/           # Scripts auxiliares executados no build
src/
├── components/      # Componentes reutilizáveis
│   ├── GameScreen.jsx
│   ├── Timer.jsx
│   ├── PlayerScore.jsx
│   └── ...
├── contexts/        # Contextos React
│   ├── SupabaseAuthContext.jsx
│   └── GameContext.jsx
├── screens/         # Telas principais
│   ├── LobbyScreen.jsx
│   ├── AuthScreen.jsx
│   ├── HomeScreen.jsx
│   └── ...
├── hooks/          # Hooks customizados
├── lib/            # Utilitários e configurações
└── pages/          # Páginas estáticas
```

Arquivos de referência para manutenção:

- `src/components/GameScreen.jsx`: tela principal da partida, permissões, eventos de timer, pontuação e chamadas de áudio.
- `src/hooks/useGameState.js`: estado online sincronizado com Supabase e atualização local do cronômetro.
- `src/hooks/useLocalGame.js`: estado local usado no modo sem autenticação.
- `src/hooks/useAudioManager.js`: inicialização e execução dos áudios e bipes.
- `public/sound/`: arquivos `Parte-1.mp3` a `Parte-5.mp3` usados nas chamadas da partida.

## 🚀 Funcionalidades Principais

### ✅ Implementadas
- [x] Sistema de autenticação completo
- [x] Cronômetro de 30 minutos com controles
- [x] Sistema de pontuação Gateball (0→1→2→3→5→0)
- [x] Gerenciamento de equipes
- [x] Sincronização em tempo real
- [x] Interface responsiva
- [x] Controle de permissões (capitão vs visualização)
- [x] Alertas sonoros e visuais
- [x] Wake lock durante jogos

## 🔊 Chamadas de Áudio

O jogo usa um cronômetro regressivo de 30 minutos (`1800` segundos). As chamadas de áudio são disparadas em `src/components/GameScreen.jsx` e executadas por `src/hooks/useAudioManager.js`.

| Período da partida | Tempo decorrido | Tempo restante | Arquivo/alerta |
| --- | ---: | ---: | --- |
| Início | 0 min | 30 min | `Parte-1.mp3` |
| Meio da partida | 15 min | 15 min | `Parte-2.mp3` |
| Terço final | 20 min | 10 min | `Parte-3.mp3` |
| Reta final | 25 min | 5 min | `Parte-4.mp3` |
| Encerramento | 30 min | 0 min | `Parte-5.mp3` |

Também existem alertas sintéticos gerados pela Web Audio API:

- Alerta nos minutos restantes `15`, `10`, `5`, `2` e `1`, sempre quando o contador chega a `00` segundos.
- Bipe a cada segundo nos últimos `10` segundos da partida.
- O áudio só é inicializado depois da primeira interação do usuário, respeitando as regras de autoplay dos navegadores.

### 🔄 Fluxo de Dados
```
Interface → Componentes → Contexts/Hooks → Supabase → PostgreSQL
                                           ↓
WebSocket ← Real-time Subscriptions ← Database Changes
```

## 🎮 Regras de Negócio

### Sistema de Pontuação
- Cada jogador começa com 0 pontos
- Sequência: 0 → 1 → 2 → 3 → 5 → 0 (cíclica)
- Soma automática por equipe
- Atualização em tempo real

### Controle de Jogo
- Apenas capitães podem controlar o timer
- Toque simples no timer para iniciar, pausar ou retomar
- Duplo toque no timer para abrir a confirmação de finalização/reinício
- Confirmação obrigatória para reset
- Timer de 30 minutos padrão

### Gerenciamento de Equipes
- Criação de equipes personalizadas
- Definição de capitães
- Controle de membros
- Restrições de exclusão (equipes em uso)

## 📊 Estados do Sistema

### Estados de Autenticação
- `loading`: Verificando sessão
- `authenticated`: Usuário logado
- `unauthenticated`: Usuário não logado

### Estados do Jogo
- `lobby`: Aguardando início
- `countdown`: Preparando para iniciar
- `running`: Jogo em andamento
- `paused`: Jogo pausado
- `completed`: Jogo finalizado

### Estados de Permissão
- `captain`: Controle completo
- `member`: Visualização + notificações
- `viewer`: Apenas visualização

## 🛠️ Tecnologias

- **Frontend**: React 18 + Vite 4
- **Styling**: Tailwind CSS + Radix UI
- **Backend**: Supabase (PostgreSQL + Auth + Realtime)
- **Animations**: Framer Motion
- **Routing**: React Router DOM
- **Icons**: Lucide React

## 📱 Compatibilidade

- ✅ Desktop (Chrome, Firefox, Safari, Edge)
- ✅ Mobile (iOS Safari, Android Chrome)
- ✅ Tablet (otimizado para uso em jogos)
- ✅ PWA ready (pode ser instalado)

## 🔐 Segurança

- Autenticação via JWT
- Row Level Security (RLS) no PostgreSQL
- Validação de permissões em tempo real
- Sanitização de dados

---

*Este documento é parte da documentação técnica da aplicação TimerGateBall Web. Para atualizações ou correções, consulte a equipe de desenvolvimento.*

# IndieFlix

# Documento de Escopo do Projeto: Plataforma de Streaming de Vídeos

## 1. Visão Geral
O projeto consiste em uma plataforma web para catálogo e reprodução de vídeos (filmes), com gerenciamento de acessos baseado em perfis (RBAC), sistema de avaliação por notas, controle de conteúdo por moderadores, postagem por cineastas independentes/administradores e experiência de streaming para usuários finais.

---

## 2. Stack Tecnológica Obrigatória

| Camada                         | Tecnologia                                                                                                     |
| :----------------------------- | :------------------------------------------------------------------------------------------------------------- |
| **Framework Web / Fullstack**  | Next.js (React)                                                                                                |
| **Runtime Backend**            | Node.js ou Bun                                                                                                 |
| **Banco de Dados**             | **PostgreSQL** ou **MongoDB**                                                                                  |
| **Ambiente Local**             | **Docker / Docker Compose** (obrigatório para orquestrar o banco de dados e/ou a aplicação em desenvolvimento) |
| **Estilização**                | Tailwind CSS                                                                                                   |
| **Autenticação e Autorização** | Better Auth                                                                                                    |


---

## 3. Perfis de Usuário e Permissões (RBAC)

A plataforma conta com três níveis de acesso bem definidos:

### 3.1. Administrador (ADM)
* Promover/rebaixar outros usuários ao papel de Administrador.
* **Postar filmes:** O ADM pode cadastrar e postar filmes no catálogo **seguindo exatamente o mesmo fluxo e campos do Filmmaker** (sem categoria especial de listagem).
* Gerenciar a lista global de **Categorias** do sistema (criar, editar e remover categorias).
* Visão global de todas as publicações da plataforma.
* **Moderação de Conteúdo:** Capacidade de **banir/suspender** um filme publicado com uma justificativa por texto (ex: violação de direitos, conteúdo inadequado). O filme fica oculto até ser corrigido.
  * *Restrição:* O ADM **não pode editar nem excluir diretamente** os filmes postados por *Filmmakers*, apenas banir/desbanir com justificativa.

### 3.2. Filmmaker (Cineasta / Criador)
* Postar seus próprios filmes.
* Definir e editar metadados dos seus filmes (título, enredo, classificação indicativa, categorias).
* Corrigir e re-submeter filmes banidos com base na justificativa fornecida pelo ADM.

### 3.3. Usuário Comum (Espectador)
* Cadastro e login na plataforma.
* Navegar pelo catálogo, filtrar por categorias e pesquisar por títulos.
* Assistir aos vídeos publicados.
* **Avaliar filmes (Atribuir Nota/Rating).**

---

## 4. Requisitos Funcionais (RF)

### RF01 — Autenticação e Gestão de Usuários
* **RF01.1:** O login é obrigatório para acessar o catálogo e visualizar vídeos.
* **RF01.2:** Autenticação implementada via **Better Auth** (e-mail e senha).
* **RF01.3:** Controle de sessão e proteção de rotas com base no perfil do usuário (`ADM`, `FILMMAKER`, `USER`).

### RF02 — Gestão do Catálogo de Filmes
* **RF02.1:** O cadastro de filmes (tanto por Filmmakers quanto por ADMs) **deve exigir obrigatoriamente** os campos:
  * **Título** (`String`)
  * **Enredo / Sinopse** (`Text`)
  * **Classificação Indicativa** (Tabela oficial brasileira: *Livre, 10, 12, 14, 16, 18 anos*)
  * **Categorias** (Múltipla escolha, ex: *Romance, Comédia, Ação, Comédia Romântica*, baseadas na lista gerida pelo ADM)
  * **Mídia de Vídeo / Capa / Banner**

### RF03 — Sistema de Avaliação (Notas)
* **RF03.1:** Usuários autenticados podem atribuir uma nota/avaliação a cada filme (ex: pontuação de 1 a 5 estrelas ou nota de 1 a 10).
* **RF03.2:** A página e os cards do filme devem exibir a **média das notas** recebidas e o número total de avaliações.
* **RF03.3:** Cada usuário pode avaliar um filme apenas uma vez (permitido atualizar sua nota existente).

### RF04 — Moderação e Fluxo de Banimento
* **RF04.1:** O ADM possui uma área de gestão onde visualiza todas as publicações da plataforma.
* **RF04.2:** Ao detectar uma irregularidade, o ADM pode aplicar o status de **Banido** ao filme, preenchendo obrigatoriamente uma **Justificativa do Banimento**.
* **RF04.3:** Filmes banidos não aparecem na listagem/busca de usuários comuns.
* **RF04.4:** O criador (Filmmaker) visualiza o motivo do banimento em seu painel e pode editar/corrigir o conteúdo para re-submissão.

### RF05 — Reprodução de Vídeo (Player)
* **RF05.1 (Mínimo):** Exibição simples do arquivo de vídeo via tag HTML5 `<video>` ou player padrão.

---

## 5. Requisitos Não-Funcionais e UI/UX

* **RNF01 — Layout:** Design responsivo adaptável a desktops, tablets e dispositivos móveis.
* **RNF02 — Performance:** Carregamento otimizado de imagens de capa e metadados.
* **RNF03 — Segurança:** Sanitização de entradas, proteção contra SQL Injection/XSS e endpoints de upload seguros.

---

## 6. Diferenciais e Critérios para Pontuação Extra (Bônus)

| Categoria                                     | Funcionalidade Bônus                                                                                                                                                                                                                                           |
| :-------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Progresso e Histórico**                     | O player salvar o tempo onde o usuário parou o vídeo ("Continuar assistindo") e manter um histórico dos últimos filmes assistidos.                                                                                                                             |
| **Séries / Sequências**                       | Suporte para organizar conteúdos em sequências/continuações de filmes ou no formato de séries com temporadas e episódios.                                                                                                                                      |
| **Comentários**                               | Sistema de comentários nas páginas dos filmes (usuários comentam; Filmmaker visualiza o feedback dos seus filmes).                                                                                                                                             |
| **Cadastro Geral de Pessoas (Ficha Técnica)** | Banco global gerenciado pelo ADM para cadastrar **Pessoas** (Atores, Diretores, Dubladores, etc.) com suas respectivas *tags/funções*. Permite ao Filmmaker/ADM vincular esse elenco/equipe ao filme.                                                          |
| **Sistema de Recomendação Inteligente**       | Algoritmo de recomendação personalizado para o usuário na Home/Catálogo com base em: <br> • Categorias mais assistidas/avaliadas pelo usuário <br> • Filmmakers/Criadores mais assistidos <br> • Pessoas envolvidas (Diretores/Atores associados ao histórico) |
| **Autenticação**                              | Suporte a **SSO Social** via Better Auth (Google, GitHub, etc.).                                                                                                                                                                                               |
| **Interface (UI)**                            | Alternância de **Modo Light / Dark** (Tema claro/escuro).                                                                                                                                                                                                      |
| **Interface (UX)**                            | Design e experiência inspirados em plataformas como **Netflix, Prime Video ou HBO Max** (carrosséis, banners de destaque, efeito hover com preview).                                                                                                           |
| **Streaming de Mídia**                        | Transmissão por **streaming** (suporte a HTTP Range Requests / chunking de vídeo ou protocolo HLS/DASH) para reprodução fluida.                                                                                                                                |
| **Player Customizado**                        | **Player de vídeo dedicado** feito do zero ou customizado (controle de qualidade, atalhos de teclado, barra de progresso personalizada, ajuste de velocidade).                                                                                                 |

---

## 7. Sugestão de Modelagem de Dados (Opcional / Não Obrigatória)

> **Nota:** A estrutura abaixo é **estritamente sugestiva**. Não há obrigação de seguir exatamente estes nomes de entidades ou campos, ficando a cargo do desenvolvedor definir a modelagem mais eficiente para a tecnologia de banco de dados escolhida (PostgreSQL ou MongoDB).

```
[ User ]
  - id, name, email, role (ADM | FILMMAKER | USER), createdAt

[ Category ]
  - id, name, slug

[ Person ] (Opcional / Bônus - Ficha Técnica)
  - id, name, photoUrl, bio
  - roles (Array: "ACTOR", "DIRECTOR", "VOICE_ACTOR", etc.)

[ Movie ]
  - id, title, synopsis, ageRating, videoUrl, thumbnailUrl, status (ACTIVE | BANNED)
  - banReason (text, nullable)
  - creatorId (FK -> User)  // Pode ser um Filmmaker OU um ADM
  - categories (Many-to-Many -> Category)
  - people (Many-to-Many -> Person, opcional)

[ Rating ]
  - id, score (Int 1-5 ou 1-10), movieId (FK -> Movie), userId (FK -> User), createdAt

[ Comment ] (Opcional / Bônus)
  - id, content, movieId (FK -> Movie), userId (FK -> User), createdAt

[ WatchHistory / Progress ] (Opcional / Bônus)
  - id, userId (FK -> User), movieId (FK -> Movie), progressSeconds, lastWatchedAt
```

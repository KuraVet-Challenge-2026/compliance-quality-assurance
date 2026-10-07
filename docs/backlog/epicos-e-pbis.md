# Backlog do KuraVet — Épicos e PBIs

Backlog reorganizado por **jornada de usuário** (Sprint 4), em substituição ao backlog organizado por tecnologia. Esta é a fonte da verdade; o arquivo [import-azure-boards.csv](import-azure-boards.csv) é gerado a partir dele para importação manual no Azure Boards (processo Scrum).

- **Perfis:** Tutor (aplicativo mobile) e Veterinário (portal web da clínica)
- **Estimativa:** campo Effort, sequência de Fibonacci (1, 2, 3, 5, 8, 13)
- **Trabalho técnico** (Oracle, PL/SQL, triggers, pipeline, MongoDB, .NET, Java) aparece como *Task* filha do PBI de negócio que ele viabiliza
- **Area Path:** `KuraVet` · **Iteration Path:** `KuraVet\Sprint 4`

## Resumo

| # | Épico | PBI | Effort | Owner |
|---|-------|-----|--------|-------|
| 1 | Acesso seguro à plataforma | Criar minha conta de tutor | 3 | Guilherme Macedo Martins |
| 2 | Acesso seguro à plataforma | Entrar na plataforma com e-mail e senha | 3 | Pedro Henrique Luiz Alves Duarte |
| 3 | Cadastro e cuidado dos meus pets | Cadastrar um pet | 3 | Henrique Martins Oliveira |
| 4 | Cadastro e cuidado dos meus pets | Consultar e alterar os dados do meu pet | 3 | Pedro Henrique Luiz Alves Duarte |
| 5 | Cadastro e cuidado dos meus pets | Remover um pet do meu cadastro | 2 | Henrique Martins Oliveira |
| 6 | Encontrar atendimento veterinário | Buscar veterinários disponíveis | 5 | Guilherme Macedo Martins |
| 7 | Encontrar atendimento veterinário | Solicitar teleconsulta para um pet | 8 | Pedro Henrique Luiz Alves Duarte |
| 8 | Encontrar atendimento veterinário | Receber orientação de triagem a partir do relato do meu pet | 8 | Henrique Martins Oliveira |
| 9 | Encontrar atendimento veterinário | Acompanhar o andamento das minhas solicitações | 3 | Guilherme Macedo Martins |
| 10 | Atender teleconsultas na clínica | Ver a fila de solicitações de teleconsulta da clínica | 5 | Pedro Henrique Luiz Alves Duarte |
| 11 | Atender teleconsultas na clínica | Aprovar uma solicitação de teleconsulta | 3 | Henrique Martins Oliveira |
| 12 | Atender teleconsultas na clínica | Recusar uma solicitação informando o motivo | 3 | Guilherme Macedo Martins |
| 13 | Atender teleconsultas na clínica | Emitir diagnóstico ao final da teleconsulta | 8 | Pedro Henrique Luiz Alves Duarte |
| 14 | Histórico de saúde do pet | Acompanhar o histórico de consultas e diagnósticos do meu pet | 5 | Henrique Martins Oliveira |

**Total:** 62 pontos — Guilherme Macedo Martins: 14 pts (4 PBIs); Pedro Henrique Luiz Alves Duarte: 27 pts (5 PBIs); Henrique Martins Oliveira: 21 pts (5 PBIs)

## Cobertura das áreas de QA

| Área | PBI |
|------|-----|
| Autenticação / login | Entrar na plataforma com e-mail e senha |
| Autocadastro de tutor | Criar minha conta de tutor |
| CRUD de pets | Cadastrar um pet; Consultar e alterar os dados do meu pet; Remover um pet do meu cadastro |
| Solicitar teleconsulta | Solicitar teleconsulta para um pet |
| Aprovar solicitação | Aprovar uma solicitação de teleconsulta |
| Recusar com motivo | Recusar uma solicitação informando o motivo |
| Emitir diagnóstico | Emitir diagnóstico ao final da teleconsulta |
| Triagem por IA | Receber orientação de triagem a partir do relato do meu pet |

---

## Épico 1 — Acesso seguro à plataforma

Jornada de quem chega ao KuraVet: o tutor cria sua conta pelo aplicativo e tutores e veterinários entram na plataforma com seus dados de acesso, vendo apenas o que é do seu perfil.

### Criar minha conta de tutor

- **Effort:** 3
- **Owner:** Guilherme Macedo Martins

**Descrição:** Como tutor, quero me cadastrar sozinho no aplicativo informando meus dados, para que eu possa usar o KuraVet sem depender de ninguém.

**Critérios de aceite:**

```gherkin
Cenário: Cadastro realizado com sucesso
Dado que não possuo conta no KuraVet
Quando informo nome, e-mail válido e senha e confirmo o cadastro
Então vejo a mensagem de conta criada
E consigo entrar no aplicativo com o e-mail e a senha informados

Cenário: E-mail já cadastrado
Dado que já existe uma conta com o e-mail que vou informar
Quando tento concluir o cadastro
Então vejo a mensagem de que o e-mail já está em uso
E nenhuma nova conta é criada

Cenário: Dados obrigatórios inválidos
Dado que estou na tela de cadastro
Quando deixo o nome em branco ou informo um e-mail em formato inválido ou uma senha curta demais
Então vejo a indicação do campo com erro e o motivo
E o cadastro não é concluído
```

**Tasks técnicas:**

- **Modelar e criar tabelas de usuário e tutor no Oracle** — Script de criação das tabelas, chaves e restrições de unicidade de e-mail. *(Owner: Guilherme Macedo Martins)*
- **Implementar o serviço de cadastro de tutor em Java (Spring Boot)** — Regras de validação e gravação do tutor, com senha armazenada de forma protegida. *(Owner: Guilherme Macedo Martins)*

### Entrar na plataforma com e-mail e senha

- **Effort:** 3
- **Owner:** Pedro Henrique Luiz Alves Duarte

**Descrição:** Como usuário da plataforma (tutor ou veterinário), quero entrar com meu e-mail e minha senha, para que eu acesse somente as funções do meu perfil.

**Critérios de aceite:**

```gherkin
Cenário: Login de tutor
Dado que sou um tutor com conta ativa
Quando informo e-mail e senha corretos no aplicativo
Então acesso a tela inicial do tutor

Cenário: Login de veterinário
Dado que sou um veterinário com conta ativa
Quando informo e-mail e senha corretos no portal da clínica
Então acesso a fila de solicitações da clínica

Cenário: Credenciais incorretas
Dado que estou na tela de login
Quando informo uma senha incorreta ou um e-mail não cadastrado
Então vejo a mensagem de e-mail ou senha inválidos
E não sou autenticado

Cenário: Acesso a área de outro perfil
Dado que estou autenticado como tutor
Quando tento abrir uma página exclusiva do portal da clínica
Então o acesso é negado e não vejo nenhum dado da clínica
```

**Tasks técnicas:**

- **Implementar autenticação com token e perfis de acesso** — Geração e validação de token, separando perfis TUTOR e VETERINARIO. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Configurar pipeline de build e deploy no Azure DevOps** — Pipeline que compila e publica o serviço de autenticação e o portal para que o login possa ser testado em ambiente na nuvem. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Registrar trilha de auditoria de acessos (trigger no Oracle)** — Trigger que registra tentativas de login e alterações de dados sensíveis. *(Owner: Pedro Henrique Luiz Alves Duarte)*

---

## Épico 2 — Cadastro e cuidado dos meus pets

Jornada do tutor para manter atualizado o cadastro dos seus animais: incluir, consultar, alterar e remover pets.

### Cadastrar um pet

- **Effort:** 3
- **Owner:** Henrique Martins Oliveira

**Descrição:** Como tutor, quero cadastrar meu pet com nome, espécie, raça e data de nascimento, para que ele possa ser atendido pelos veterinários.

**Critérios de aceite:**

```gherkin
Cenário: Pet cadastrado com sucesso
Dado que estou autenticado como tutor
Quando informo nome, espécie, raça e data de nascimento e salvo
Então o pet aparece na minha lista de pets

Cenário: Campos obrigatórios em branco
Dado que estou na tela de novo pet
Quando tento salvar sem informar o nome ou a espécie
Então vejo a indicação dos campos obrigatórios
E o pet não é cadastrado

Cenário: Data de nascimento no futuro
Dado que estou na tela de novo pet
Quando informo uma data de nascimento posterior a hoje
Então vejo a mensagem de data inválida
E o pet não é cadastrado
```

**Tasks técnicas:**

- **Modelar tabela de pets e vínculo com o tutor no Oracle** — Tabela PET com chave estrangeira para o tutor e restrições de domínio para espécie. *(Owner: Henrique Martins Oliveira)*
- **Implementar cadastro de pets no aplicativo (React Native)** — Formulário com validações e integração com o serviço de pets. *(Owner: Henrique Martins Oliveira)*

### Consultar e alterar os dados do meu pet

- **Effort:** 3
- **Owner:** Pedro Henrique Luiz Alves Duarte

**Descrição:** Como tutor, quero ver a lista dos meus pets e corrigir suas informações, para que o cadastro reflita sempre a realidade do animal.

**Critérios de aceite:**

```gherkin
Cenário: Listar meus pets
Dado que tenho dois pets cadastrados
Quando abro a tela Meus pets
Então vejo os dois pets com nome e espécie
E não vejo pets de outros tutores

Cenário: Alteração salva
Dado que estou na tela de detalhes de um dos meus pets
Quando altero o nome e salvo
Então vejo o novo nome na lista e nos detalhes

Cenário: Alteração inválida
Dado que estou editando um pet
Quando apago o nome e tento salvar
Então vejo a indicação de campo obrigatório
E os dados anteriores permanecem
```

**Tasks técnicas:**

- **Implementar consulta e atualização de pets no serviço Java** — Listagem filtrada pelo tutor autenticado e atualização com validação de propriedade. *(Owner: Pedro Henrique Luiz Alves Duarte)*

### Remover um pet do meu cadastro

- **Effort:** 2
- **Owner:** Henrique Martins Oliveira

**Descrição:** Como tutor, quero excluir um pet que não faz mais parte da minha família, para que meu cadastro fique organizado.

**Critérios de aceite:**

```gherkin
Cenário: Pet removido
Dado que tenho um pet sem consultas em andamento
Quando peço para excluí-lo e confirmo a exclusão
Então o pet deixa de aparecer na minha lista

Cenário: Exclusão cancelada
Dado que pedi para excluir um pet
Quando cancelo na tela de confirmação
Então o pet continua na minha lista

Cenário: Pet com teleconsulta em andamento
Dado que meu pet tem uma solicitação de teleconsulta pendente ou aprovada
Quando tento excluí-lo
Então vejo a mensagem de que não é possível excluir enquanto houver atendimento em andamento
E o pet continua cadastrado
```

**Tasks técnicas:**

- **Criar procedure de exclusão lógica de pet com verificação de consultas abertas (PL/SQL)** — Procedure que bloqueia a exclusão quando houver solicitação pendente ou aprovada. *(Owner: Henrique Martins Oliveira)*

---

## Épico 3 — Encontrar atendimento veterinário

Jornada do tutor desde a dúvida sobre a saúde do pet até o pedido de teleconsulta: buscar veterinários, descrever o problema, receber uma orientação inicial e acompanhar o pedido.

### Buscar veterinários disponíveis

- **Effort:** 5
- **Owner:** Guilherme Macedo Martins

**Descrição:** Como tutor, quero buscar veterinários por nome ou especialidade, para que eu escolha o profissional mais adequado ao meu pet.

**Critérios de aceite:**

```gherkin
Cenário: Busca com resultados
Dado que existem veterinários cadastrados na especialidade Dermatologia
Quando busco por Dermatologia
Então vejo a lista dos veterinários dessa especialidade com nome e clínica

Cenário: Busca sem resultados
Dado que nenhum veterinário atende a especialidade que digitei
Quando realizo a busca
Então vejo a mensagem de nenhum veterinário encontrado

Cenário: Perfil do veterinário
Dado que estou vendo a lista de resultados
Quando seleciono um veterinário
Então vejo seus dados profissionais e a opção de solicitar teleconsulta
```

**Tasks técnicas:**

- **Criar consulta de veterinários por nome e especialidade no Oracle** — View e índices para busca por nome e especialidade. *(Owner: Guilherme Macedo Martins)*
- **Implementar tela de busca no aplicativo (React Native)** — Campo de busca, lista e perfil do veterinário. *(Owner: Guilherme Macedo Martins)*

### Solicitar teleconsulta para um pet

- **Effort:** 8
- **Owner:** Pedro Henrique Luiz Alves Duarte

**Descrição:** Como tutor, quero solicitar uma teleconsulta para um dos meus pets com um veterinário escolhido, para que eu receba atendimento sem sair de casa.

**Critérios de aceite:**

```gherkin
Cenário: Solicitação enviada
Dado que sou tutor com um pet cadastrado e escolhi um veterinário
Quando seleciono o pet, descrevo o motivo da consulta e envio a solicitação
Então vejo a confirmação do envio
E a solicitação aparece com status Pendente

Cenário: Motivo não informado
Dado que estou na tela de solicitação
Quando tento enviar sem descrever o motivo
Então vejo a indicação de campo obrigatório
E a solicitação não é enviada

Cenário: Solicitação repetida
Dado que já existe uma solicitação pendente do mesmo pet com o mesmo veterinário
Quando tento enviar outra
Então vejo a mensagem de que já há uma solicitação em análise
E nenhuma nova solicitação é criada

Cenário: Sem pets cadastrados
Dado que não tenho nenhum pet cadastrado
Quando abro a tela de solicitação
Então sou orientado a cadastrar um pet antes de continuar
```

**Tasks técnicas:**

- **Criar tabela de solicitações e regras de status no Oracle** — Modelagem de SOLICITACAO_CONSULTA com status Pendente, Aprovada, Recusada e Concluída. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Criar procedure de abertura de solicitação com validação de duplicidade (PL/SQL)** — Impede segunda solicitação pendente do mesmo pet com o mesmo veterinário. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Implementar serviço de solicitações em Java (Spring Boot)** — Criação e consulta de solicitações. *(Owner: Pedro Henrique Luiz Alves Duarte)*

### Receber orientação de triagem a partir do relato do meu pet

- **Effort:** 8
- **Owner:** Henrique Martins Oliveira

**Descrição:** Como tutor, quero descrever em texto os sintomas do meu pet e receber uma orientação inicial de urgência, para que eu saiba se devo buscar atendimento com rapidez.

**Critérios de aceite:**

```gherkin
Cenário: Triagem realizada
Dado que estou na tela de solicitação de teleconsulta
Quando descrevo os sintomas do pet e peço a triagem
Então vejo uma classificação de urgência (baixa, média ou alta) com recomendação
E vejo o aviso de que a orientação não substitui a avaliação do veterinário

Cenário: Relato curto demais
Dado que estou na tela de triagem
Quando envio um relato com menos de 10 caracteres
Então vejo a mensagem pedindo mais detalhes
E a triagem não é executada

Cenário: Serviço de triagem indisponível
Dado que o serviço de triagem está fora do ar
Quando peço a triagem
Então vejo a mensagem de que a orientação não está disponível no momento
E consigo seguir com a solicitação de teleconsulta mesmo assim

Cenário: Resultado visível ao veterinário
Dado que fiz a triagem e enviei a solicitação
Quando o veterinário abre a solicitação
Então ele vê o relato e a classificação de urgência
```

**Tasks técnicas:**

- **Implementar microsserviço .NET de triagem por IA do relato** — Recebe o texto do tutor, consulta o modelo de IA e devolve classificação de urgência. *(Owner: Henrique Martins Oliveira)*
- **Armazenar relatos e resultados de triagem no MongoDB** — Coleção com relato, classificação e data, vinculada à solicitação. *(Owner: Henrique Martins Oliveira)*
- **Integrar o serviço de triagem ao serviço Java de solicitações** — Chamada entre serviços com tratamento de indisponibilidade. *(Owner: Henrique Martins Oliveira)*

### Acompanhar o andamento das minhas solicitações

- **Effort:** 3
- **Owner:** Guilherme Macedo Martins

**Descrição:** Como tutor, quero ver o status de cada teleconsulta que solicitei, para que eu saiba se foi aprovada ou recusada e por quê.

**Critérios de aceite:**

```gherkin
Cenário: Solicitação aprovada
Dado que o veterinário aprovou minha solicitação
Quando abro a lista de solicitações
Então vejo o status Aprovada ao lado da solicitação

Cenário: Solicitação recusada
Dado que o veterinário recusou minha solicitação informando um motivo
Quando abro os detalhes da solicitação
Então vejo o status Recusada e o motivo informado

Cenário: Sem solicitações
Dado que ainda não fiz nenhuma solicitação
Quando abro a lista de solicitações
Então vejo a mensagem de que não há solicitações
```

**Tasks técnicas:**

- **Expor consulta de solicitações do tutor no serviço Java** — Listagem com status e motivo de recusa, filtrada pelo tutor autenticado. *(Owner: Guilherme Macedo Martins)*

---

## Épico 4 — Atender teleconsultas na clínica

Jornada do veterinário no portal da clínica: ver os pedidos recebidos, aceitar ou recusar com justificativa e registrar o diagnóstico do atendimento.

### Ver a fila de solicitações de teleconsulta da clínica

- **Effort:** 5
- **Owner:** Pedro Henrique Luiz Alves Duarte

**Descrição:** Como veterinário, quero ver as solicitações pendentes direcionadas a mim, para que eu possa decidir quais atender.

**Critérios de aceite:**

```gherkin
Cenário: Fila com solicitações
Dado que existem solicitações pendentes para mim
Quando abro a fila no portal da clínica
Então vejo as solicitações com pet, tutor, motivo e urgência
E as mais antigas aparecem primeiro

Cenário: Fila vazia
Dado que não há solicitações pendentes
Quando abro a fila
Então vejo a mensagem de que não há solicitações pendentes

Cenário: Solicitações de outro veterinário
Dado que existem solicitações pendentes destinadas a outro veterinário
Quando abro minha fila
Então não vejo essas solicitações
```

**Tasks técnicas:**

- **Construir tela da fila de solicitações no portal web da clínica** — Lista paginada com ordenação por data. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Criar consulta da fila por veterinário no Oracle** — View de solicitações pendentes por veterinário com dados de pet e tutor. *(Owner: Pedro Henrique Luiz Alves Duarte)*

### Aprovar uma solicitação de teleconsulta

- **Effort:** 3
- **Owner:** Henrique Martins Oliveira

**Descrição:** Como veterinário, quero aprovar uma solicitação pendente, para que o tutor saiba que o atendimento será realizado.

**Critérios de aceite:**

```gherkin
Cenário: Aprovação realizada
Dado que tenho uma solicitação pendente aberta
Quando clico em Aprovar
Então o status muda para Aprovada
E a solicitação sai da fila de pendentes
E o tutor passa a ver o status Aprovada

Cenário: Solicitação já decidida
Dado que a solicitação já foi aprovada ou recusada
Quando tento aprová-la novamente
Então vejo a mensagem de que a solicitação já foi analisada
E o status não é alterado
```

**Tasks técnicas:**

- **Criar procedure de aprovação de solicitação com controle de transição de status (PL/SQL)** — Só permite aprovar solicitações no status Pendente. *(Owner: Henrique Martins Oliveira)*

### Recusar uma solicitação informando o motivo

- **Effort:** 3
- **Owner:** Guilherme Macedo Martins

**Descrição:** Como veterinário, quero recusar uma solicitação explicando o motivo, para que o tutor entenda a decisão e busque outra alternativa.

**Critérios de aceite:**

```gherkin
Cenário: Recusa com motivo
Dado que tenho uma solicitação pendente aberta
Quando clico em Recusar, escrevo o motivo e confirmo
Então o status muda para Recusada
E o tutor consegue ver o motivo informado

Cenário: Recusa sem motivo
Dado que estou na janela de recusa
Quando tento confirmar sem escrever o motivo
Então vejo a indicação de que o motivo é obrigatório
E a solicitação continua Pendente

Cenário: Solicitação já decidida
Dado que a solicitação já foi aprovada
Quando tento recusá-la
Então vejo a mensagem de que a solicitação já foi analisada
E o status não é alterado
```

**Tasks técnicas:**

- **Criar procedure de recusa com motivo obrigatório (PL/SQL)** — Grava o motivo e valida a transição de status. *(Owner: Guilherme Macedo Martins)*

### Emitir diagnóstico ao final da teleconsulta

- **Effort:** 8
- **Owner:** Pedro Henrique Luiz Alves Duarte

**Descrição:** Como veterinário, quero registrar o diagnóstico e as orientações do atendimento, para que o tutor receba o resultado da consulta e ele fique guardado no histórico do pet.

**Critérios de aceite:**

```gherkin
Cenário: Diagnóstico emitido
Dado que tenho uma teleconsulta aprovada
Quando informo diagnóstico e orientações e confirmo a emissão
Então a solicitação passa para Concluída
E o diagnóstico fica disponível ao tutor e no histórico do pet

Cenário: Diagnóstico em branco
Dado que estou na tela de emissão de diagnóstico
Quando tento confirmar sem preencher o diagnóstico
Então vejo a indicação de campo obrigatório
E a consulta continua Aprovada

Cenário: Consulta não aprovada
Dado que a solicitação está Pendente ou Recusada
Quando tento emitir diagnóstico
Então a opção não está disponível ou é negada com mensagem explicativa

Cenário: Diagnóstico já emitido
Dado que a consulta já está Concluída
Quando tento emitir um novo diagnóstico
Então vejo a mensagem de que o diagnóstico já foi emitido
E o diagnóstico original não é alterado
```

**Tasks técnicas:**

- **Modelar tabela de diagnósticos no Oracle** — Tabela DIAGNOSTICO vinculada à solicitação, com texto e orientações. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Criar procedure de emissão de diagnóstico e conclusão da consulta (PL/SQL)** — Grava diagnóstico e atualiza status em uma única transação. *(Owner: Pedro Henrique Luiz Alves Duarte)*
- **Construir tela de emissão de diagnóstico no portal web** — Formulário de diagnóstico e orientações. *(Owner: Pedro Henrique Luiz Alves Duarte)*

---

## Épico 5 — Histórico de saúde do pet

Jornada do tutor para acompanhar a vida clínica do seu pet: consultas realizadas e diagnósticos recebidos em um só lugar.

### Acompanhar o histórico de consultas e diagnósticos do meu pet

- **Effort:** 5
- **Owner:** Henrique Martins Oliveira

**Descrição:** Como tutor, quero ver todas as consultas e diagnósticos já recebidos por cada um dos meus pets, para que eu tenha o histórico de saúde sempre à mão.

**Critérios de aceite:**

```gherkin
Cenário: Histórico com consultas
Dado que meu pet teve teleconsultas concluídas
Quando abro o histórico do pet
Então vejo as consultas em ordem da mais recente para a mais antiga, com data, veterinário e diagnóstico

Cenário: Pet sem histórico
Dado que meu pet nunca foi atendido
Quando abro o histórico do pet
Então vejo a mensagem de que ainda não há consultas

Cenário: Histórico de pet de outro tutor
Dado que estou autenticado como tutor
Quando tento abrir o histórico de um pet que não é meu
Então o acesso é negado e nenhum dado é exibido
```

**Tasks técnicas:**

- **Criar consulta de histórico por pet no Oracle** — View com consultas concluídas e diagnósticos ordenados por data. *(Owner: Henrique Martins Oliveira)*
- **Construir tela de histórico do pet no aplicativo (React Native)** — Linha do tempo de consultas e detalhe do diagnóstico. *(Owner: Henrique Martins Oliveira)*

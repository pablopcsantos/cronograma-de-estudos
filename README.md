# Ferramenta de Cronogramas de Estudo

Aplicação web educacional estática para **criar, editar, adaptar e acompanhar cronogramas de estudo** diretamente no navegador.

A ferramenta foi projetada para ser reutilizável: o usuário pode trabalhar com diferentes cursos, provas, disciplinas ou rotinas de estudo por meio de cronogramas estruturados em JSON. O cronograma **“Semiextensivo TEP 2026 - Turma de Abril” da MedCof** está incorporado apenas como conjunto de dados demonstrativo e como exemplo prático de utilização da aplicação.

O projeto funciona de forma autossuficiente em um único arquivo `index.html`: interface, estilos, lógica JavaScript, editor de cronogramas e cronograma-padrão ficam incorporados no próprio HTML. O arquivo `README.md` existe apenas para documentação do repositório.

> **Projeto independente e não oficial.** Não há vínculo, afiliação, patrocínio ou endosso da MedCof ou de outras instituições cujos cronogramas venham a ser utilizados na ferramenta.

---

## Finalidade

A aplicação foi desenvolvida para transformar um cronograma de estudos em uma ferramenta interativa de planejamento e acompanhamento. Entre os principais objetivos estão:

- permitir a criação e edição de cronogramas estruturados;
- acompanhar atividades concluídas;
- adaptar o planejamento ao tempo disponível;
- reorganizar prioridades sem apagar o cronograma original;
- retirar atividades individualmente ou por classes de prioridade;
- criar exceções individuais para atividades que devem permanecer no plano;
- calcular o percentual global de progresso;
- importar e exportar cronogramas e progresso em JSON;
- manter os dados localmente, sem depender de backend.

---

## Arquitetura

O webapp foi deliberadamente construído para manter toda a aplicação em um único arquivo executável:

```text
/
├── index.html
└── README.md
```

No `index.html` estão incorporados:

- estrutura HTML da interface;
- estilos CSS dos modos claro e escuro;
- JavaScript da aplicação;
- cronograma-padrão em JSON;
- editor e validador de cronogramas;
- gerenciamento de prioridades;
- importação e exportação de dados;
- visualização “Sobre o projeto”.

O `README.md` documenta o projeto no GitHub e não é necessário para a execução do webapp.

---

## Funcionalidades

### Cronograma por blocos

As atividades são organizadas em blocos expansíveis. Cada item pode apresentar:

- título;
- especialidade ou categoria;
- data de liberação;
- prioridade;
- situação da liberação;
- estado de conclusão;
- estado de inclusão no plano ativo.

### Modo escuro por padrão

A aplicação inicia em **modo escuro**. O usuário pode alternar para o modo claro pelo botão correspondente.

A preferência é armazenada localmente no navegador.

### Data inicial ajustável

O usuário pode alterar a data de início do cronograma.

Quando essa data é modificada, as demais datas de liberação são deslocadas proporcionalmente, preservando os intervalos relativos do cronograma original.

### Atividades concluídas

Cada atividade possui um checkbox para ser marcada como concluída/vista.

A alteração atualiza automaticamente:

- número de atividades concluídas;
- número de atividades removidas do plano;
- número de pendências;
- percentual de progresso;
- barra visual de progresso.

### Remoção individual — ✂️

O botão **✂️** permite retirar uma atividade individualmente do plano.

A atividade permanece visível, de modo que o usuário continue tendo referência do cronograma completo. A ação pode ser revertida a qualquer momento.

### Classes de prioridade

Na área interna **Gerenciar cronograma**, o usuário pode escolher quais classes de prioridade fazem parte do plano ativo.

O formato atualmente reconhece:

| Prioridade | Interpretação padrão |
|---|---|
| Diamante | Assistir primeiro |
| Verde | Assistir após as diamantes |
| Amarela | Assistir após as verdes |
| Vermelha | Assistir após as amarelas |
| Bônus | Conteúdo adicional |

Desmarcar uma classe **não apaga nem oculta seus itens**. As atividades continuam no cronograma principal e recebem a marcação:

**⏸ Fora do plano**

Mesmo fora do plano, uma atividade ainda pode ser marcada como concluída caso o usuário decida realizá-la.

### Exceção individual — 📌

O botão **📌** permite manter uma atividade específica no plano mesmo quando toda a classe de prioridade à qual ela pertence foi desmarcada.

Nessa situação, a atividade recebe a indicação:

**📌 Mantida no plano**

Esse mecanismo permite fazer adaptações finas sem precisar reativar uma classe inteira.

### Alteração individual de prioridade

A prioridade de cada atividade pode ser modificada diretamente na tela principal.

As alterações são consideradas pelos filtros e salvas no progresso local.

### Cálculo de progresso

O dashboard apresenta:

- Total;
- Concluídas/Vistas;
- Removidas;
- Pendentes;
- Progresso (%).

O sistema utiliza o conceito de **atividade tratada**. Uma atividade é tratada quando:

1. foi marcada como concluída/vista;
2. foi removida individualmente com ✂️; ou
3. pertence a uma classe temporariamente fora do plano e não foi mantida como exceção com 📌.

A fórmula é:

```text
Progresso (%) = atividades tratadas / total de atividades × 100
```

Se uma atividade fora do plano for posteriormente marcada como concluída, ela passa a ser contabilizada entre as concluídas, e não entre as removidas.

---

## Gerenciar cronograma

A aplicação possui uma segunda visualização interna chamada **Gerenciar cronograma**. Ela não é um arquivo HTML separado: permanece dentro do mesmo `index.html`.

Essa área permite:

- ativar e desativar classes de prioridade;
- exportar o cronograma atual em JSON;
- importar um cronograma JSON;
- carregar o cronograma no editor interno;
- validar um cronograma antes de aplicá-lo;
- aplicar um cronograma criado pelo usuário;
- restaurar o cronograma-padrão incorporado.

---

## Criando um cronograma próprio

Um cronograma válido pode ter a seguinte estrutura:

```json
{
  "versao": 1,
  "cronograma_id": "meu-cronograma-2026",
  "nome": "Meu cronograma de estudos",
  "data_inicio": "2026-04-06",
  "aulas": [
    {
      "id": 1,
      "bloco": "Bloco 1",
      "data_liberacao": "06/04/2026",
      "aula": "Tema ou atividade 1",
      "especialidade": "Área A",
      "prioridade": "Diamante"
    },
    {
      "id": 2,
      "bloco": "Bloco 1",
      "data_liberacao": "06/04/2026",
      "aula": "Tema ou atividade 2",
      "especialidade": "Área B",
      "prioridade": "Verde"
    }
  ]
}
```

### Campos do objeto principal

| Campo | Obrigatório | Descrição |
|---|---|---|
| `versao` | Não | Versão informativa do formato |
| `cronograma_id` | Sim | Identificador estável do cronograma |
| `nome` | Sim | Nome do cronograma |
| `data_inicio` | Sim | Data inicial em `AAAA-MM-DD` |
| `aulas` | Sim | Lista não vazia de atividades |

### Campos de cada atividade

| Campo | Obrigatório | Descrição |
|---|---|---|
| `id` | Sim | Identificador único da atividade |
| `bloco` | Sim | Agrupamento exibido na interface |
| `data_liberacao` | Sim | Data em `DD/MM/AAAA` |
| `aula` | Sim | Título da atividade |
| `especialidade` | Sim | Categoria, área ou especialidade |
| `prioridade` | Sim | Classe de prioridade reconhecida |

Prioridades aceitas pela versão atual:

```text
Diamante
Verde
Amarela
Vermelha
Bônus
```

### Regras práticas para editar JSON

- use aspas duplas;
- não inclua comentários dentro do JSON;
- não deixe vírgula após o último item;
- use um `id` único para cada atividade;
- use um `cronograma_id` diferente para cronogramas diferentes;
- preserve IDs existentes quando quiser manter a associação com o progresso já salvo.

Uma forma segura de começar é exportar o cronograma atual, criar uma cópia e utilizá-la como modelo.

---

## Validação dos cronogramas

Antes de aplicar um cronograma importado ou editado, o sistema verifica, entre outros pontos:

- se o conteúdo principal é um objeto JSON;
- existência e tipo de `cronograma_id`;
- existência e tipo de `nome`;
- formato de `data_inicio`;
- existência de uma lista não vazia de atividades;
- presença de IDs;
- IDs duplicados;
- preenchimento dos campos textuais obrigatórios;
- formato das datas de liberação;
- uso de prioridades reconhecidas.

Um arquivo que não satisfaça essas verificações não é aplicado pela ferramenta.

Essa verificação é estrutural e não representa validação acadêmica ou científica do conteúdo inserido pelo usuário.

---

## Separação do progresso por cronograma

O campo `cronograma_id` é utilizado para separar o estado de cronogramas diferentes.

Cada cronograma pode manter independentemente:

- atividades concluídas;
- atividades removidas;
- prioridades alteradas;
- exceções com 📌;
- classes de prioridade fora do plano;
- data inicial escolhida.

Isso reduz o risco de aplicar o progresso de um cronograma a outro conjunto de atividades.

---

## Persistência local

A aplicação utiliza `localStorage` para salvar automaticamente o estado do usuário no navegador.

Podem ser armazenados localmente:

- atividades concluídas;
- atividades removidas com ✂️;
- alterações de prioridade;
- exceções individuais com 📌;
- classes de prioridade fora do plano;
- data inicial;
- cronograma personalizado importado;
- preferência de tema.

O projeto não possui backend próprio e não sincroniza esses dados automaticamente entre dispositivos.

---

## Importação e exportação de progresso

A tela principal disponibiliza:

- **💾 Exportar progresso**
- **📂 Importar progresso**

A exportação gera um arquivo JSON com o estado do usuário para o cronograma atual.

Ao importar, a aplicação verifica o `cronograma_id` para evitar aplicar um arquivo de progresso pertencente a outro cronograma.

Essa funcionalidade também permite transportar manualmente o estado entre computadores ou navegadores.

---

## Exemplo incorporado: MedCof Semiextensivo TEP 2026

Para demonstrar a ferramenta em uma situação real, o `index.html` inclui como cronograma-padrão o **Semiextensivo TEP 2026 - Turma de Abril** da MedCof.

As informações desse cronograma foram obtidas no site da própria empresa:

[https://cronograma.grupomedcof.com.br/](https://cronograma.grupomedcof.com.br/)

O uso desse conjunto de dados neste projeto tem finalidade **meramente educativa e exemplificativa**, servindo de base para demonstrar a criação de um webapp de organização de cronogramas de estudo.

Essa inclusão também pode ser útil a estudantes que estejam se preparando para o TEP utilizando especificamente esse curso. Entretanto, **a ferramenta não depende desse cronograma** e pode receber cronogramas de outros cursos, provas, instituições ou rotinas pessoais de estudo por meio da importação ou edição de JSON.

O cronograma-padrão atualmente incorporado contém 121 atividades distribuídas em 18 agrupamentos.

Este projeto é independente e não oficial. Não há vínculo, afiliação, patrocínio ou endosso da MedCof. Marcas, nomes e conteúdos de terceiros permanecem de responsabilidade de seus respectivos titulares.

---

## Sobre o projeto

A visualização interna **Sobre o projeto** apresenta:

- autoria;
- finalidade da ferramenta;
- transparência sobre o uso de inteligência artificial generativa;
- Currículo Lattes;
- origem do cronograma utilizado como exemplo;
- aviso de independência do projeto.

Ela permanece integrada ao mesmo `index.html`.

---

## Como executar

Não há dependências a instalar.

Basta abrir:

```text
index.html
```

em um navegador moderno.

Também é possível executar o projeto por meio de um servidor HTTP estático, como a extensão **Live Server** do Visual Studio Code, ou publicá-lo em serviços de hospedagem de arquivos estáticos.

---

## Publicação no GitHub Pages

Como a aplicação é estática e o arquivo principal se chama `index.html`, o repositório pode ser publicado pelo GitHub Pages diretamente a partir da branch principal.

No GitHub:

1. abra **Settings** do repositório;
2. entre em **Pages**;
3. selecione a publicação a partir de uma branch;
4. escolha a branch principal e a pasta raiz;
5. salve a configuração.

O README não é necessário para a execução da página, mas será exibido pelo GitHub na página principal do repositório quando estiver na raiz. A documentação do GitHub confirma que READMEs na raiz são reconhecidos e apresentados automaticamente. 

---

## Privacidade

O funcionamento principal ocorre localmente no navegador.

O projeto não implementa:

- contas de usuário;
- autenticação;
- backend próprio;
- banco de dados remoto;
- sincronização automática em nuvem.

A portabilidade dos dados é feita manualmente pelos recursos de importação e exportação JSON.

---

## Tecnologias utilizadas

- **JavaScript** — lógica da aplicação, persistência, importação/exportação, validação e cálculo de progresso;
- **HTML5** — estrutura e interface;
- **CSS3** — apresentação, responsividade e temas;
- **JSON** — representação de cronogramas e arquivos de intercâmbio.

O projeto não depende de frameworks ou bibliotecas JavaScript externas para seu funcionamento básico.

---

## Compatibilidade

O projeto utiliza APIs nativas disponíveis em navegadores modernos, entre elas:

- `localStorage`;
- `FileReader`;
- `Blob`;
- `URL.createObjectURL`;
- manipulação do DOM.

Recomenda-se utilizar versões atuais de navegadores como Chrome, Edge, Firefox ou equivalentes.

---

## Limitações conhecidas

- O progresso é local e não é sincronizado automaticamente entre dispositivos.
- Alterar o `id` de uma atividade pode romper a associação com o progresso anteriormente armazenado.
- Alterar o `cronograma_id` cria intencionalmente um novo espaço de progresso.
- A ferramenta valida a estrutura do JSON, mas não a correção acadêmica do conteúdo inserido.
- Cronogramas provenientes de terceiros podem ser atualizados na fonte original sem que o exemplo incorporado seja atualizado automaticamente.
- O projeto não substitui cronogramas oficiais, orientações pedagógicas ou informações publicadas pelas instituições responsáveis pelos cursos utilizados pelo usuário.

---

## 👤 Autoria e desenvolvimento

Aplicação web educacional desenvolvida de forma independente por **Pablo Phillipe Cândido dos Santos**, destinada à criação, edição, adaptação e acompanhamento de cronogramas de estudo. A ferramenta permite organizar atividades por blocos e prioridades, acompanhar o progresso e importar ou exportar cronogramas estruturados em JSON.

O desenvolvimento contou com a utilização de ferramentas de inteligência artificial generativa como recurso auxiliar no processo de desenvolvimento, mantendo-se sob responsabilidade do autor a concepção, implementação, integração e verificação do projeto.

Currículo Lattes: [http://lattes.cnpq.br/9500873674712528](http://lattes.cnpq.br/9500873674712528)

---

## Licenciamento

Nenhuma licença de software foi adicionada automaticamente a este repositório. A escolha de uma licença define permissões de uso, modificação e redistribuição e deve ser feita conscientemente pelo autor antes da publicação, caso se deseje conceder essas permissões.

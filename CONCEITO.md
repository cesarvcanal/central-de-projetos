# O modelo de conhecimento

A Central de Projetos é, no fundo, um **grafo de conhecimento operacional**. Este documento explica as camadas e as entidades — o *conceito*, não a implementação. Sem regra de negócio, sem código.

## A regra de ouro: função única por camada

O erro clássico de uma base de conhecimento é deixar tudo virar "documento" ou "nota". Aí uma decisão vira uma nota, uma tarefa vira outra nota, um pensamento solto vira mais uma — e nada tem estrutura, versionamento ou fluxo próprio. Vira um pântano de texto.

A Central evita isso com uma regra dura: **cada tipo de nó tem uma função que nenhum outro tem.** Se você está em dúvida sobre onde algo entra, a pergunta certa é "qual é a natureza disso?", não "onde eu jogo isso?".

| Camada | Pergunta que ela responde | Por que não se confunde com as outras |
|---|---|---|
| **Decisão** | "Como deve ser?" | É uma definição normativa. Não é trabalho (tarefa), não é dúvida (ideia). Tem versionamento próprio: decisão nova *substitui* a antiga, sem apagá-la. |
| **Tarefa** | "O que precisa ser feito?" | Tem status, responsável e andamento. É a única camada com fluxo de trabalho. Uma decisão pode *gerar* tarefas, mas não *é* uma tarefa. |
| **Ideia** | "E se…?" | É um pensamento não comprometido. Não polui a lista de tarefas com coisas que talvez nunca aconteçam. Pode ser *promovida* a tarefa quando amadurece. |
| **Documento** | "O que se sabe sobre isso?" | Conhecimento durável e estruturado (arquitetura, guia, mapa). Não expira como uma tarefa nem é normativo como uma decisão. |
| **Aprendizado** | "O que aprendemos resolvendo isso?" | Um problema + a lição. Diferente de decisão porque *muda com o tempo* (a lição evolui) e é descritivo, não normativo. Diferente de documento porque tem o par problema→lição e versionamento. |
| **Input** | "Quem falou o quê, exatamente?" | A fala literal, append-only. É a proveniência — a matéria-prima de onde os outros nós são destilados. Nunca se reescreve. |
| **Commit** | "O que mudou no código?" | Liga o grafo de conhecimento ao grafo de código. Ingerido do repositório, não escrito à mão. |

## As três naturezas de um nó

Olhando por cima, os nós se agrupam em três naturezas — e essa distinção é o que dá clareza ao modelo:

- **Normativo** (como *deve* ser): a **Decisão**. Versionável, porque o que "deve ser" muda — e o histórico do que mudou importa.
- **Operacional** (o que *está* acontecendo): a **Tarefa**, com seu ciclo de status e andamento.
- **Descritivo / de proveniência** (o que *foi dito* ou *aprendido*): **Input**, **Aprendizado**, **Documento**, **Ideia**. Registram a realidade e o conhecimento, cada um numa granularidade diferente — do literal (input) ao destilado (documento).

## Versionamento e proveniência

Duas propriedades atravessam o modelo inteiro:

**Append-only na proveniência.** O input — a fala original — nunca é editado. Se a interpretação estava errada, anexa-se uma correção; o original permanece. Isso garante que sempre dá pra voltar à fonte e entender de onde uma decisão saiu. A memória não pode ser reescrita retroativamente.

**Substituição versionada nas camadas normativas.** Uma decisão não é editada por cima quando muda de ideia — cria-se uma **nova** decisão que *substitui* a anterior, e a anterior fica marcada como substituída, apontando para a nova. O mesmo vale para o aprendizado, que pode ser marcado como *superado* por uma versão mais atual. O resultado é uma trilha completa: dá pra ver não só o que se pensa hoje, mas a evolução de como se chegou lá.

## As arestas: onde mora o valor

Uma lista de nós é um banco de dados. O que faz disto um **grafo** — e o que dá o poder de recuperar contexto em segundos — são as arestas tipadas entre os nós:

- `origina` — este input originou esta decisão; esta decisão originou estas tarefas.
- `implementa` — este commit implementa esta tarefa.
- `depende` / `integra` / `consome` — relações entre projetos e módulos.
- `substitui` — esta decisão substitui aquela; este aprendizado supera aquele.
- `relacionado` — vínculo leve, estilo wikilink, entre documentos e temas.

Seguindo as arestas, um agente (ou pessoa) parte de "por que decidimos X?" e chega, sem adivinhar, à conversa original, às tarefas que saíram dali, ao código que as implementou e aos aprendizados que ficaram. **O contexto não é procurado — é percorrido.**

## Camadas de organização (o continente dos nós)

Os nós vivem dentro de contêineres hierárquicos, aninháveis:

- **Projeto** — a unidade maior. Pode representar *algo que se constrói* (um sistema, um produto) ou *uma área que se opera* (um setor da empresa, com seus agrupamentos internos).
- **Agrupamento** — subdivisão aninhável dentro de um projeto (áreas, módulos, frentes de trabalho).

O contêiner é também onde vive o **controle de acesso**, resolvido em três camadas independentes que não se sobrepõem:

1. **Pode?** — a capacidade global (papel no sistema). Define o teto do que a pessoa/agente consegue fazer em qualquer lugar.
2. **Onde?** — a concessão por entidade (projeto, agrupamento ou tarefa específica), no estilo de um grafo de compartilhamento: leitura ou edição, concedida a uma **pessoa ou a um agente**.
3. **O quê?** — a visibilidade do conteúdo de cada nó (privado / compartilhado / público). O **default é privado** — uma trava deliberada, para que abrir algo seja sempre um ato consciente.

Essa separação é o que permite dizer, com precisão, "este agente lê o contexto de arquitetura deste projeto, mas não enxerga o conteúdo sensível" — sem inventar papéis rígidos e sem duplicar regra de acesso.

## Por que um agente de IA se encaixa naturalmente aqui

Nada no modelo foi adaptado *para* IA — e é justamente por isso que a IA se encaixa. Um agente é só mais um ator que:

- **lê** o grafo seguindo arestas para montar o contexto de um assunto;
- **classifica** um input e o grava no tipo de nó certo;
- **escreve** dentro das mesmas permissões (RBAC) de um humano, deixando a mesma auditoria e a mesma proveniência.

Um agente e uma pessoa são, para a Central, o mesmo tipo de cidadão: identidades que operam sob o mesmo modelo de acesso, sobre a mesma estrutura de conhecimento. É isso que torna a empresa **operável por conversa** — e o conhecimento, cumulativo em vez de perecível.

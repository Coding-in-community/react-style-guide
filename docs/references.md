# Referências - Ed.1, Rv.2

## Sumário:
- Organização de arquivos
- Modularização
- Definição de componentes
- Hierarquia
- ~Uso de componentes~ (seção removida)
- Nomenclatura
- Referências

## Organização de arquivos 

A organização dos componentes pode ser dividida em dois escopos principais: "global" e "local". Os componentes globais são utilizados em todo o aplicativo, enquanto os locais são específicos para determinadas telas.

Essa divisão se estende para outros elementos como hooks, services, utils e outras subcategorias, que são agrupados de acordo com seus escopos específicos. Utilizamos `shared` para elementos de escopo global e `features` para elementos de escopo local.

### Exemplo de `shared`:

```sh
src/
|-- shared/
|   |-- components/     // Componentes como buttons, modals, etc.
|   |-- utils/          // Funções utilitários
|   |-- hooks/          // React hooks
|   |-- styles/         // Estilos e temas
|-- views/              
|-- app.js
```

### Exemplo de `features`:

```sh
src/
|-- features/
|   |-- featureA/     // Componentes como buttons, modals, etc.
|       |-- components/     // Componentes como buttons, modals, etc.
|       |-- utils/          // Funções utilitários
|       |-- hooks/          // React hooks
|       |-- styles/         // Estilos e temas
|   |-- featureB/     // Componentes como buttons, modals, etc.
|       |-- components/     // Componentes como buttons, modals, etc.
|       |-- utils/          // Funções utilitários
|       |-- hooks/          // React hooks
|       |-- styles/         // Estilos e temas
|-- views/              
|-- app.js
```

`Shared` e `features` compartilham a mesma estrutura organizacional, diferindo apenas no escopo. `Shared` é considerada uma feature de escopo global. Isso permite que os desenvolvedores localizem recursos globais mais facilmente e compreendam a estrutura geral do projeto de maneira intuitiva.

Embora o conteúdo de `shared` possa teoricamente ser colocado dentro de uma feature denominada `shared`, é preferível mantê-lo na pasta raiz por razões de clareza e acessibilidade.

## Modularização

A utilização de `features` permite a separação de recursos locais em módulos independentes. No entanto, isso não implica na necessidade de criar uma feature para cada pequeno escopo da aplicação. Features são usualmente criadas para funcionalidades mais robustas, como autenticação e comunicação.

```sh
src/
|-- features/
|   |-- auth/           // Autenticação
|   |-- customers/      // Gestão de clientes
|   |-- posts/          // Publicações e artigos
|-- views/              
|-- app.js
```

Uma dica interessante é agrupar features de forma semelhate a que você agruparia rotas, onde `/auth` equivale `features/auth`, `/customers` vira `features/customers` e assim por diante.

### Considerações sobre `shared`:

- A pasta `shared` deve ser entendida como uma coleção de elementos de escopo global, mas com a característica estrutural de uma feature.
- Ela serve como um repositório para componentes, hooks, utilidades e outros elementos que têm variantes específicas por feature, mas também necessitam de uma versão genérica acessível globalmente.
- Isso significa que objetos globais que não possuem uma feature não devem ir na pasta shared por questões de consistência. 

### Diretrizes para Pastas em `features`:

- Pastas dentro de `features` devem ser exclusivas ao escopo local da feature, evitando duplicar a estrutura que é intrinsecamente global.
- Recursos que são universais e não possuem contrapartes locais não devem ser colocados dentro de `features` ou `shared`.

### Exemplos de Pastas Globais:

- `App.js`: Ponto central da aplicação e por definição não é compartilhável.
- `/config`: Contém as configurações gerais do aplicativo, não se destinando a ser subdividido por features.
- `/stores`: Centraliza o estado global da aplicação, sem necessidade de instâncias locais por feature.
- `/pages`: Define as páginas em nível de aplicação, uma estrutura que não é replicada localmente devido a preocupações com a clareza de import/export.
- `/services`: Representa a lógica de negócio e interação com APIs externas ou outros serviços, operando fora da lógica de estado do UI.

### Pastas Típicas dentro de `features`:

- `/components`: Principal pasta de uma feature, contendo elementos reutilizáveis e específicos do contexto.
- `/compounds`: Inclui componentes complexos que, apesar de sua modularidade e potencial de reutilização global, podem ser especializados por feature.
- `/containers`: Componentes que encapsulam a lógica e o estado para outros componentes filhos, segregando preocupações.
- `/layouts`: Focados na estruturação visual e espacial dos componentes, sem envolver-se diretamente com a lógica de negócios.
- `/hooks`: Custom hooks que encapsulam lógicas de estado ou efeitos secundários específicos da feature.

### Sobre `services`:

- A decisão de não incluir `services` dentro de `features` é uma preferência pessoal, fundamentada nas seguintes características:
    - **Singleton por natureza**: Uma única instância que permeia a aplicação, reforçando seu caráter global.
    - **Pertencimento a outra camada**: Enquanto os elementos de `features` são da camada de apresentação, `services` fazem parte da camada de aplicação, devendo manter-se segregados para evitar a mistura de responsabilidades.
    - **Lógica e testes específicos**: `services` têm uma natureza distinta, com testes e lógicas de comunicação que são melhor mantidos agrupados para otimizar o desenvolvimento e manutenção.

O arquiteto de software deve avaliar cuidadosamente a inclusão de pastas em `features`, recomendando-se a escolha de elementos que integrem a camada de apresentação e que demandem clara diferenciação de escopo. A consistência e a clareza devem ser as diretrizes norteadoras para facilitar a manutenção e escalabilidade do projeto.

## Definição de componentes

Os componentes são elementos fundamentais no desenvolvimento de interfaces em frameworks frontend, apresentando uma gama de padrões que se distinguem pelo propósito e pela aplicabilidade. Abaixo, segue uma revisão dos tipos de componentes com uma distinção mais clara entre suas nomenclaturas e funções.

### Common Components (Componentes de Comuns)

**Definição e Uso**: Esses componentes são a base para a construção de interfaces. Eles gerenciam tarefas como renderização de UI, estilização e podem manter estado interno quando necessário. Sua responsabilidade principal é a exibição de elementos de UI e a interação com o usuário.

**Características Distintivas**:
- Foco em elementos visuais e interatividade.
- Podem ser estado-dependentes (stateful) ou independentes (stateless).
- Geralmente servem como wrappers para elementos nativos de HTML e outros componentes de apresentação.

### Composite Components (Componentes Compostos)

**Definição e Uso**: Os componentes compostos são uma técnica de design em que um novo componente é formado pela combinação de vários componentes menores, possibilitando a reutilização e a manutenção mais fácil do código. Este padrão é útil para construir componentes de alta ordem que encapsulam comportamentos complexos ou estruturas de UI componíveis.

**Características Distintivas**:
- Cada componente menor é responsável por sua própria lógica e apresentação, enquanto o componente composto gerencia a coordenação entre eles.
- Comportamentos complexos e interações entre componentes menores são encapsulados dentro do componente composto, criando uma abstração limpa e de fácil compreensão.

### Container Components (Componentes Container)

**Definição e Uso**: Containers são componentes que abstraem a gestão de lógica de negócios e estados da aplicação. Eles agem como intermediários entre a fonte de dados (como APIs) e os componentes de apresentação, focando na operacionalização dos dados.

**Características Distintivas**:
- Separam a lógica de negócios da UI, promovendo a responsabilidade única.
- Conectam-se a serviços e stores, manipulando dados e estados antes de passar para a UI.

### Layout Components (Componentes de Layout)

**Definição e Uso**: Estes componentes concentram-se em estruturar e organizar o espaço da interface de usuário. Sua função é puramente estrutural, não contendo lógica de negócios, mas ajudando a manter um design responsivo e consistente.

**Características Distintivas**:
- Fornecem uma estrutura para outros componentes sem gerenciar estado ou lógica.
- Exemplos incluem wrappers para grids, espaçamento e alinhamento.

### Higher-Order Components (HOCs - Componentes de Ordem Superior)

**Definição e Uso**: HOCs são funções que aceitam um componente e retornam um novo componente com funcionalidades adicionais. Eles seguem o princípio de composição sobre herança para estender o comportamento dos componentes sem alterar sua implementação original.

**Características Distintivas**:
- Facilitam a reutilização de código e lógica comum entre componentes.
- Permitem a adição de novos comportamentos, como manipulação de erros ou injeção de props, sem modificar o componente original.

Cada um desses padrões de componentes tem seu lugar em um ecossistema React, ajudando desenvolvedores a criar interfaces claras, eficientes e mantíveis, respeitando os princípios de design como composição, reutilização e separação de preocupações.

## Hierarquia

Embora o React não prescreva uma hierarquia rígida por definição, a adoção de modelos como o Atomic Design pode proporcionar uma estrutura lógica para organizar componentes. Vamos adaptar a hierarquia de componentes sugerida para seguir as práticas recomendadas, proporcionando uma ordem previsível sem sacrificar a flexibilidade do React.

### Componentes Comuns (`common`)

- **Definição:** Equivalente aos átomos, são os elementos mais simples e indivisíveis da UI, como botões, ícones, inputs de texto e links.
- **Exemplo:** Um botão de 'enviar' em um formulário.

### Componentes Compostos (`composites`)

- **Definição:** Funcionam como as moléculas e os organismos, combinando múltiplos componentes base ou outros compostos para formar unidades funcionais mais complexas.
- **Exemplo:** Uma barra de navegação completa com logotipo, menu de navegação e campo de busca.

### Layouts e Containers (`layouts` e `containers`)

- **Definição:** Correspondem aos templates, onde o layout se concentra no estilo e o container engloba a lógica.
- **Exemplo:** Um layout de página de blog que define a estrutura de cabeçalho, conteúdo principal, barra lateral e rodapé.

### Páginas (`pages`)

- **Definição:** Representam as páginas finais preenchidas com conteúdo real, apresentadas ao usuário.
- **Exemplo:** Uma página de blog específica com conteúdo autêntico, imagens e comentários.

## Nomenclatura

Todos os componentes devem possuir ao menos duas palavras. Componentes globais devem conter App ou Base como primeiro nome, enquanto componentes locais devem possuir o nome da página a qual pertencem 

- AppButton (common component)
- AppForm (compound component)
- HomeNavbar (compound component)
- AboutLayout (layout component)
- AppFormContainer (container component)
- FormPage (page component)

## Referências 

React Folder Structure 
https://youtu.be/UUga4-z7b6s?si=_o43kFZ_SwcQshbc

React Compound Components
https://medium.com/unibuddy-technology-blog/compound-components-in-react-b04772f9eb58

Atomic Design 
https://medium.com/pretux/atomic-design-o-que-%C3%A9-como-surgiu-e-sua-import%C3%A2ncia-para-a-cria%C3%A7%C3%A3o-do-design-system-e3ac7b5aca2c

Vue Style Guide
https://vuejs.org/style-guide/

AirBnB Style Guide
https://github.com/airbnb/javascript

Vue for Large Projects
https://vueschool.io/articles/vuejs-tutorials/how-to-structure-a-large-scale-vue-js-application/

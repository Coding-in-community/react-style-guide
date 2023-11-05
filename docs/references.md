# Referências - Ed.1, Rv.0

## Sumário:
- Organização de arquivos
- Modularização
- Tipo de componentes
- Hierarquia
- Uso de componentes
- Nomenclatura
- Referências

## Organização de arquivos 

Podemos dividir o escopo dos componentes entre "global" e "local". Componentes globais são utilizados ao longo de todo aplicativo, e componentes locais são especificos a determinadas telas.

Dito isso, essa lógica não se aplica apenas aos componentes. Podemos estender essa ideia para outras coisas como hooks, services, utils e uma sequência de outras subpastas, agrupando de acordo com seus determinados escopos. Para isso, utilizamos `shared` e 
`features`.

Exemplo de shared:
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

Exemplo de features:
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

Como é possivel perceber, `shared` e `features` compartilham da mesma organização, a unica diferença é de fato o escopo. Shared é **apenas mais uma feature**, mas com escopo global. Isso permite que:
- O programador encontre os recursos globais mais facilmente
- Seja mais simples para o programador entender a organização geral de outras features

Teoricamente poderiamos colocar o conteúdo dentro da pasta shared dentro de uma features sob mesmo nome `shared` => `features/shared`, porém é preferivel deixar na pasta raiz pelos motivos já citados.


## Modularização

Ao utilizar `features` estamos separando recursos locais em espaços limitados, porém, isso não significa que devemos criar uma feature nova para cada pequeno escopo dentro da nossas aplicação. Por exemplo, caso tenhamos uma página simples, com apenas um componente, criar uma feature completa é desperdicio de tempo e espaço.

Features são comumente criadas para autenticação, localização, comunicação e etc. São literalmente pequenos módulos contidos dentro da sua própria aplicação. Via de regra, tudo que não é global pertence a alguma feature, cabe a você escolher a feature correta. 

```sh
src/
|-- features/
|   |-- auth/     
|   |-- customers/     
|   |-- posts/    
|-- views/              
|-- app.js
```

Uma dica interessante é agrupar features de forma semelhate a que você agruparia rotas, onde `/auth` vira `features/auth`, `/customers` vira `features/customers` e assim por diante. Não é uma bala de prata, mas já é um começo.  

Outra coisa importante é quais pastas colocar dentro das features (incluindo shared). Nós temos que ter em mente o seguinte:
- Shared, apenas de representar conteúdo global, ainda é uma feature;
- Features, por serem locais, não devem possuir pastas que podem existir global;
- Logo, shared deve conter recursos global que possuem uma contrapartida local. 

Eu sei, é confuso, mas é importante manter a consistencia sempre. Alguns exemplos de coisas que nunca vão dentro de features, por tanto, nunca dentro de shared.
- `App.js` - É um recurso único e não compartilhavel.
- `/config` - Arquivo de configuração da aplicação.
- `/stores` - São globais por padrão, não existindo uma versão local aplicável às features
- `/pages` - Também são globais por padrão, versões locais não são aconselhaveis devido ao ciclo de import/export.
- `/services` - Lógica que não depende do estado interno da aplicação.

Features comuns:
- `/components` - Facilmente o mais comum e provavelmente o principal motivo da criação de features
- `/compounds` - Componentes complexos com alta modularidade. Por serem modulares, geralmente acabam sendo criados globalmente, porém, é uma estrutura que pode ser facilmente reaproveitada pare componentes especificos. 
- `/container` - Wrapper lógico. Componente criado para abstrair a lógica de componentes filhos.
- `/layout` - Wrapper para estilos. Componente criado para organizar a disposição de elementos.
- `/hooks` - Hooks customizados. Abstrai a lógica de componentes em funções externas dependentes de estado

Uma coisa que você deve ter percebido é que `services` não vão dentro de features. Essa é uma escolha pessoal minha, baseada nas seguintes caracteristicas:
- Services são singletons por natureza. Eles tem apenas uma unica instancia ao longo de toda a aplicação, o que os torna, em essencia, objetos globais.
- Services fazem parte de outra camada. Enquanto components, compounds, containers, layout e hooks fazem parte da camada de aprensentação, services fazem parte da camada de aplicação. Camadas diferentes não devem ser misturadas. 
- Services possuem sua própria lógica. Seus testes são diferentes, a forma que se comunica com o interior também. Agrupar services juntos é benefico para o seu próprio desenvolvimento, uma vez que eles trabalham de forma diferente do resto da aplicação

Existem varias e varias pastas que podem ir ou não dentro das features, cabe ao arquiteto decidir qual a abordagem mais correta. A recomendação é que sempre sejam objetos que façam parte da camada de apresentação e que possuam multiplas delimitações de escopo.

## Tipo de componentes

Componentes, por definição, são peças extremamente versáteis do desenvolvimento em qualquer framework frontend, isso quer dizer que há várias formas diferentes de obter o mesmo resultado e tecnicas para isso. Felizmente, a partir da repetição foram surgindo padrões de componentes, os quais passaram e ter própositos bem definidos.

### Componentes Comuns

Além desse, é claro, há o componente comum, da forma que conhecemos. Ele é especificamente eficaz em lidar com lógica simples e estilos. Ele geralmente serve como wrapper para outros componentes e elementos comuns do html. Pode ou não possuir estado.

### Compound Component

Compound components são um padrão em React que permitem que vários componentes trabalhem juntos de forma implícita, compartilhando um estado comum. Essa técnica é útil quando temos um conjunto de componentes que devem ser usados juntos, como um `select` e `option`. O componente pai controla o estado compartilhado, e os componentes filhos se inscrevem nesse estado, permitindo uma comunicação indireta e controle por parte do componente pai.

### Container Component

O componente container lida principalmente com a lógica de negócios e o gerenciamento de estado da aplicação, delegando a renderização da parte visual para os componentes de apresentação. Eles são responsáveis ​​por buscar dados, manipular estados e implementar funções que alteram esses estados. Este padrão ajuda a separar as preocupações de lógica e UI, facilitando o teste e a manutenção.

### Layout Component

Componentes de layout são aqueles que ajudam a organizar o espaço na interface do usuário. Eles não estão diretamente ligados à lógica de negócios, mas sim à estrutura visual da aplicação. Exemplos incluem componentes para grids, espaçamentos, alinhamento de elementos e outros aspectos estruturais do layout. Eles são usados para envolver outros componentes e auxiliar na criação de um design consistente e responsivo.

### High Order Component

Um High Order Component é uma técnica avançada em React para reutilizar a lógica de componentes. HOCs são funções que recebem um componente e retornam um novo componente com comportamento adicional. Eles são como funções puras em JavaScript e podem ser usados para adicionar funcionalidades como manipulação de erros, log de renderização, controle de acesso, e injeção de props, entre outras. Isso permite que os componentes sejam escritos de maneira mais declarativa e funcional.

### Hierarquia

Por definição não existe algum hierarquia comum para o React, ainda assim há conceitos como o design atomic que ditam uma ordem lógica de como os componentes devem ser usados. Resumidamente, ele descreve a seguinte divisão:
1. **Átomos:**
    - São os elementos básicos e indivisíveis da UI, como botões, ícones, inputs de texto e links.
    - Exemplo: Um botão de 'enviar' em um formulário.
2. **Moléculas:**
    - Combinam múltiplos átomos para formar uma unidade funcional mais complexa.
    - Exemplo: Um campo de formulário com um input de texto, um ícone e um botão de 'enviar'.
3. **Organismos:**
    - Agrupamentos de moléculas que funcionam juntos como parte de uma seção da interface.
    - Exemplo: Uma barra de navegação completa, que inclui o logotipo da empresa, um menu de navegação (moléculas) e um campo de busca (outra molécula).
4. **Templates:**
    - Combinações de organismos que formam a estrutura de uma página, mas sem conteúdo final.
    - Exemplo: O layout de uma página de blog, incluindo cabeçalho, conteúdo principal, barra lateral e rodapé, todos definidos em suas posições relativas.
5. **Páginas:**
    - Instâncias de templates preenchidos com conteúdo real, representando o que o usuário finalmente vê.
    - Exemplo: Uma página de blog específica com o cabeçalho, o artigo real, imagens, comentários e rodapé.

Por mais que seja uma boa separação de conceitos, ela peca em se adequar no uso comum do React. É algo novo, não uma simples evolução do que já existe. Para isso, eu sugiro a seguinte interpretação:

1. Componentes Comuns (`components`)
	1. Equivalem a atomos na sua forma mais básica
	2. Podem representar moleculas quando necessário pouca personalização e controle 
2. Components Compostos (`compounds`)
	1. Caso necessário, podem representar moleculas
	2. Equivalem a organismos em sua forma mais básica
3. Containers (`layouts`e `containers`)
	1. Equivalem a templates
	2. Layout é encapsula estilo, container encapsula lógica

Com essa definição, podemos aplicar o design atomico, porém sem mudar a estrutura comum do React. Assim podemos impor controle sobre a hierarquia de componentes de forma previsivel. 

Ainda há alguns pontos a serem explorados, como o que fazer caso um compound seja usado varias vezes de forma semelhante. Por mais que um layout seja capaz de abstrair a repetição, talvez não seja a melhor das formas. Fora isso, não existe uma restrição para uso de componentes de baixo nível, assim como uma página pode usar o template ela também pode usar um atomo. 

## Uso de componentes

Acima já ficou claro o escopo de cada tipo componente de acordo com o design atomico, abaixo segue um guia com sugestões de acordo com a necessidade. 

1. Botão
	1. Component: Criado como o elemento mais básico com estilos e funcionalidades elementares como clicar.
	2. Compound: Pode envolver um ícone ou um indicador de carregamento, combinando mais de um átomo.
2. Input
	1. Component: Campo de texto simples, com validações e estilos básicos.
	2. Compound: Pode incluir uma label, ícone de validação ou mensagem de erro.
3. Input Group
	1. Component: Combina vários componentes, como um input, botão e ícone em uma única unidade funcional.
	2. Compound: Pode fazer parte de um formulário de pesquisa mais complexo com diferentes tipos de inputs e botões.
4. Form
	1. Compound: Composto por vários componentes como inputs, botões e mensagens de erro que trabalham juntos para coletar informações do usuário.
	2. Container: Em contextos mais amplos, pode ser parte de um template que define como o formulário interage com outros componentes da página.
5. Card
	1. Component: Pode conter um título, uma imagem e uma breve descrição ou tags.
	2. Compound: Um card mais complexo pode incluir botões de ação, um slider de imagens ou um formulário de comentário.
6. Table
	1. Compound: Complexa por natureza, inclui átomos como botões e inputs, bem como cabeçalho de tabela, linhas e células.
	2. Layout: Pode se transformar em um template quando combinada com outros organismos como filtros de pesquisa, paginação e controles.
7. Navbar
	1. Compounds: Uma barra de navegação com links (components) e dropdowns (compounds), e até mesmo um formulário de busca.
	2. Layout: Quando integrado a um cabeçalho maior com branding e outros controles de navegação.
8. Dashboard
	1. Layout: Uma combinação de vários compound como gráficos, tabelas, e sumários.
	2. Página: Quando preenchido com dados reais e utilizado dentro do contexto da aplicação.

É importante notar que, apesar da navbar poder ser contruida usando apenas componentes, é recomendável usar um compound. Isso porque o dropdown também é um compound.

Um componente pode ser usado apenas por aqueles no mesmo nível ou em nível superior. Isso quer dizer que um compound não deve ser importado dentro de um component. Isso serve para manter a hierarquia limpa e bem ordenada.

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

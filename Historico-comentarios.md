# Histórico de Comentários

## Visão Geral
Em alguns projetos é necessário criar uma seção em que os usuários podem adicionar comentários e esses comentários deverão ser mostrados em display mode em ordem cronológica.

Os usuários deverão acessar um novo form (ver item "Form para adicionar um novo comentário") para adicionar um novo comentário clicando em um ícone (ver item Ícones), assim que adicionarem poderão visualizar o novo comentário
em display mode na tela do aplicativo dentro de uma gallery.

De forma geral, a hierarquia de elementos será:

### Tela

A Tela é o lugar onde todas os controles (forms, labels, buttons, gallery, etc...) estarão situados e organizados. 

**Atente-se**: Use a propriedade **OnVisible** para configurar as variáveis e pré-carregar os dados usados na tela.


### Gallery para mostrar os comentários
Dentro da tela adicione o controle "Gallery" onde os comentários serão mostrados em linha do tempo. 

**Data source**: Deverá ser a lista em que os comentários serão buscados.
**Propriedade Items**: A conexão de dados será feita através da lista que selecionou em Data source, para filtrar os comentários de acordo com o ID, você terá que personalizar o filtro na propriedade "Items".
Para personalizar o filtro adicione a função "Sort" dentro de items.

`Sort( Filter(Nome da lista, Id=""), Coluna, Ordem)`

**Lista**: Data source

**Formula**: Defina por quais IDs e colunas o filtro deverá ser definido

**Ordem**: Defina se os registros deverão aparecer em ordem crescente ou decrescente.

Link de referência sobre como usar a função Sort: https://docs.microsoft.com/pt-br/powerapps/maker/canvas-apps/functions/function-sort

### Form para adicionar um novo comentário

Na tela deverá ter um ícone (adicionar) para abrir um novo formulário em que o usuário poderá adicionar um novo comentário.
**Controle a visibilidade desse novo form na propriedade "Visible"** 

Este novo form deverá fazer conexão com a mesma lista (Data source) definida na gallery - onde os comentários serão adicionados.

**Propiedade importante para validar quando uma operação de dados: `OnSuccess`**
Se o novo item for criado com sucesso, a propriedade OnSuccess será executada.

#### Funções dentro de OnSuccess:

ForAll: A fórmula pode calcular um valor e/ou realização ações, como modificar dados.

Patch e Defaults: Use a função `Patch` com a função `Defaults` para criar registros. 

É recomendável usar o operador  ThisRecord para resolver o nome do campo que será atualizado e evitar ambiguidade.

**Exemplo**: 

` // Para cada item na coleção ou para cada variável, comparamos seu Id com o title e atualizamos cada registro correspondente com o último comentário.`

`ForAll(Coleção/Variável,
    Patch(Data source,
    Defaults(Data source ),
    { Analistas:{'@odata.type': "#Microsoft.Azure.Connectors.SharePoint.SPListExpandedReference",
     Id: ThisRecord.ID,
     Value: ThisRecord.Title
   } },
    { Id_Comentario: Form para adicionar um novo comentário.LastSubmit.ID } );`

### Ícones

**Adicionar:** O ícone Adicionar deverá estar localizado na tela principal, onde o usuário irá clicar para adicionar um novo comentário no Form.

Propriedade **OnSelect**: Aqui o usuário deverá ser redirecionado para o novo form, use a função NewForm()
`NewForm(Form para adicionar um novo comentário)`

**Salvar:** Se o seu app precisar enviar uma notificação ou executar um fluxo, sua fórmula deve estar inserida na propriedade "OnSelect" do ícone.
O ícone também deverá submeter o novo form e definir variáveis para fechar o form:

`NOME DO FLUXO.Run(
   // Defina corpo e variáveis que deverão ser enviadas
);
SubmitForm(Form para adicionar um novo comentário) ;
Set(variaveis para fechar o form - exemplo: Visibilidade,false) ;  Refresh(Data source)`

**ATENTE-SE: DEFINA BEM AS VARIÁVEIS QUE O FLUXO DEVE RECEBER E QUE O FORM DEVE ATUALIZAR**

**Cancelar:** Para cancelar o registro de um novo comentário insira o ícone cancelar com a função `Back()` na propriedade **OnSelect**

---
lab:
  title: Explorar o catálogo de modelos no Estúdio de IA do Azure
---

# Explorar o catálogo de modelos no Estúdio de IA do Azure

O catálogo de modelos do Estúdio de IA do Azure serve como um repositório central onde você pode explorar e usar uma variedade de modelos, facilitando a criação do cenário de IA generativa.

Neste exercício, você explorará o catálogo de modelos no Estúdio de IA do Azure.

> **Observação**: O Estúdio de IA do Azure está em versão prévia no momento em que este artigo foi escrito e sob desenvolvimento ativo. Alguns elementos do serviço podem não ser exatamente como descritos, e alguns recursos podem não funcionar como esperado.

> Para concluir este exercício, sua assinatura do Azure deve ser aprovada para acesso ao serviço do OpenAI do Azure.

Este exercício levará aproximadamente **25** minutos.

## Criar um Hub de IA do Azure

Você precisa de um Hub de IA do Azure na sua assinatura do Azure para hospedar projetos. É possível criar esse recurso ao criar um projeto ou provisioná-lo com antecedência (que é o que faremos neste exercício).

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure.

1. Na seção Gerenciamento, selecione "Todos os hubs" e, em seguida, selecione **+ Novo hub**. Crie um novo hub com as seguintes configurações:
    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Criar um novo grupo de recursos com um nome exclusivo ou selecionar um já criado*
    - **Localização**: *faça uma escolha **aleatória** de uma das regiões a seguir*\*
        - Leste da Austrália
        - Leste do Canadá
        - Leste dos EUA
        - Leste dos EUA 2
        - França Central
        - Leste do Japão
        - Centro-Norte dos EUA
        - Suécia Central
        - Norte da Suíça
        - Sul do Reino Unido
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: Selecione para criar um novo Serviço de IA ou usar um existente
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota em cenários em que você está compartilhando um locatário com outros usuários. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

1. Selecione **Criar**. A criação do primeiro hub pode levar alguns minutos para ser concluída. Durante a criação do hub, os seguintes recursos de IA também serão criados para você: 
    - Serviços de IA
    - Conta de armazenamento
    - Key vault

1. Depois que o Hub de IA do Azure for criado, ele deverá ter uma aparência semelhante à da imagem a seguir:

    ![Captura de tela dos detalhes do Hub de IA do Azure no Estúdio de IA do Azure.](./media/azure-ai-overview.png)

## Criar um projeto

Um Hub de IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto em seu Hub de IA do Azure.

1. No Estúdio de IA do Azure, na página **Compilar**, selecione **+ Novo projeto**. Em seguida, no assistente **Criar um novo projeto**, crie um projeto com as seguintes configurações:

    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
    - **Hub**: *Seu Hub de AI*

1. Aguarde até que seu projeto seja criado. O resultado deve ser similar à imagem a seguir:

    ![Captura de tela de uma página de detalhes do projeto no Estúdio de IA do Azure.](./media/azure-ai-project.png)

1. Exiba as páginas no painel do lado esquerdo, expandindo cada seção, e observe as tarefas que você pode executar e os recursos que você pode gerenciar em um projeto.

## Implantar um modelo

Agora você está pronto para implantar um modelo a ser usado por meio do **Estúdio de IA do Azure**. Depois de implantado, você usará o modelo para gerar conteúdo em linguagem natural.

1. No Estúdio de IA do Azure, crie uma nova implantação com as seguintes configurações:

    - **Modelo**: gpt-35-turbo
    - **Tipo de implantação**: Padrão
    - **Recurso conectado do OpenAI do Azure**: *Sua conexão com o OpenAI do Azure*
    - **Versão do Modelo**: atualização automática para padrão
    - **Nome de implantação**: *um nome exclusivo de sua preferência*
    - **Opções avançadas**
        - **Filtro de conteúdo**: Padrão
        - **Limite de taxa de tokens por minuto**: 5 mil

> **Observação**: cada modelo do Estúdio de IA do Azure é otimizado para um equilíbrio diferente entre funcionalidades e desempenho. Usaremos o modelo **GPT 3.5 Turbo** neste exercício, que é altamente capaz para cenários de chat e geração de linguagem natural.

## Testar o modelo no playground de chat

Vamos ver como o modelo se comporta em uma interação de conversa.

1. Navegue até o **Playground** no painel do lado esquerdo.

1. No modo **Chat**, insira o prompt a seguir na seção **Sessão de Chat**.

    ```
   <Placeholder>
    ```

1. O modelo provavelmente responderá com algum texto...

## Modificar a mensagem de prompt do sistema

No playground de chat, você pode modificar o prompt do sistema para alterar o comportamento do modelo. O prompt do sistema é a mensagem inicial que o modelo recebe antes de começar a gerar respostas.

1. Na seção **Mensagem do sistema**, altere a mensagem do sistema adotando o seguinte texto:

    ```
    <Placeholder>
    ```

1. Aplique as alterações na mensagem do sistema.

1. Na seção **Sessão de chat**, insira novamente a solicitação a seguir.

    ```
   <Placeholder>
    ```

8. Observe a saída, que deve indicar que...


## Adicionar dados no playground de chat

<Placeholder>

## Limpar

Quando terminar de usar o recurso OpenAI do Azure, lembre-se de excluir a implantação ou todo o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true).


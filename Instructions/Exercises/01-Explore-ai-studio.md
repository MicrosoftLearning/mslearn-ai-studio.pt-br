---
lab:
  title: Explorar o Estúdio de IA do Azure
---

# Explorar o Estúdio de IA do Azure

Nesse exercício, você usa o IA do Azure Studio para criar um projeto e explorar um modelo de IA generativo.

> Para concluir este exercício, sua assinatura do Azure deve ser aprovada para acesso ao serviço do OpenAI do Azure. Preencha o [formulário de registro](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) para solicitar acesso aos modelos Azure OpenAI.

Este exercício levará aproximadamente **30** minutos.

## Abrir o Estúdio de IA do Azure

Vamos começar explorando o IA do Azure Studio.

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure. A página inicial do Estúdio de IA do Azure é semelhante à seguinte imagem:

    ![Captura de tela do Estúdio de IA do Azure.](./media/azure-ai-studio-home.png)

1. Examine as informações na página inicial e exiba cada uma das guias, observando as opções para explorar modelos e recursos, criar projetos e gerenciar recursos.

## Crie um hub de IA do Azure

Você precisa de um hub IA do Azure na sua assinatura do Azure para hospedar projetos. É possível criar esse recurso ao criar um projeto ou provisioná-lo com antecedência (que é o que faremos neste exercício).

1. Na seção **Gerenciamento**, selecione **Todos os hubs** e selecione **+ Novo hub**. Crie um novo hub com as seguintes configurações:
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
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: *Selecione para criar um novo serviço de IA ou usar um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota em cenários em que você está compartilhando um locatário com outros usuários. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

    Após a criação do hub IA do Azure, ele deverá ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um hub de IA do Azure no Azure AI Studio.](./media/azure-ai-resource.png)

1. Abra uma nova guia do navegador (deixando a guia do Estúdio de IA do Azure aberta) e navegue até o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true), entrando com suas credenciais do Azure, se solicitado.
1. Navegue até ao grupo de recursos onde criou o seu hub IA do Azure e veja os recursos Azure que foram criados.

    ![Captura de tela de um hub de IA do Azure e recursos relacionados no portal do Azure.](./media/azure-portal.png)

1. Retorne à guia do navegador do Estúdio de IA do Azure.
1. Veja cada uma das páginas no painel do lado esquerdo da página do seu hub IA do Azure e observe os artefatos que pode criar e gerir. Na página **Conexões**, observe que as conexões com os serviços Azure OpenAI e AI já foram criadas.

## Criar um projeto

Um hub IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto no seu hub IA do Azure.

1. Na IA do Azure Studio, verifique se você está no hub que acabou de criar (você pode verificar sua localização verificando o caminho na parte superior da tela).
1. Navegue até **Todos os projetos** usando o menu à esquerda.
1. Selecione **+ New project**.
1. No assistente **Criar um projeto**, crie um projeto com as seguintes configurações:
    - **Central atual**: *Seu hub de IA*
    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
1. Aguarde até que seu projeto seja criado. O resultado deve ser similar à imagem a seguir:

    ![Captura de tela de uma página de detalhes do projeto no Estúdio de IA do Azure.](./media/azure-ai-project.png)

1. Exiba as páginas no painel do lado esquerdo, expandindo cada seção, e observe as tarefas que você pode executar e os recursos que você pode gerenciar em um projeto.

## Implantar e testar um modelo

Você pode usar um projeto para criar soluções complexas de IA baseadas em modelos de IA generativos. Explorar completamente todas as opções de desenvolvimento disponíveis no Estúdio de IA do Azure está além do escopo deste exercício, mas veremos algumas maneiras básicas de trabalhar com modelos em um projeto.

1. No painel à esquerda do seu projeto, na seção **Componentes**, selecione a página **Implantações**.
1. Na página **Implantações**, na guia **Implantações de modelo**, selecione **+ Criar implantação**.
1. Procure o modelo **gpt-35-turbo** na lista, selecione e confirme.
1. Implante o modelo com as seguintes configurações:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Tipo de implantação**: Padrão
    - **Recurso conectado do OpenAI do Azure**: *Selecione a conexão padrão que foi criada quando você criou seu hub*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: Padrão

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 5.000 TPM são suficientes para os dados usados neste exercício.

1. Após a implantação do modelo, na página de visão geral da implantação, selecione **Abrir no playground**.
1. Na página **Chat playground**, certifique-se de que a implantação do seu modelo esteja selecionada na seção **Implantação**.
1. Na janela de chat, insira uma consulta como *O que é IA?* e veja a resposta:

    ![Captura de tela do playground no Estúdio de IA do Azure.](./media/playground.png)

## Limpeza

Se tiver terminado de explorar o Estúdio de IA do Azure, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente a [portal do Azure](https://portal.azure.com?azure-portal=true) em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

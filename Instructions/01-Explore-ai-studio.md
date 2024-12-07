---
lab:
  title: Explorar os componentes e as ferramentas do Azure AI Foundry
---

# Explorar os componentes e as ferramentas do Azure AI Foundry

Nesse exercício, você usará o portal do Azure AI Foundry para criar um projeto e explorar um modelo de IA generativa.

Este exercício levará aproximadamente **30** minutos.

## Abra o portal do Azure AI Foundry

Vamos começar explorando o portal do Azure AI Foundry.

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure. A página inicial do Azure AI Foundry é semelhante à seguinte imagem:

    ![Captura de tela do portal do Azure AI Foundry.](./media/azure-ai-studio-home.png)

1. Examine as informações na página inicial e exiba cada uma das guias, observando as opções para explorar modelos e recursos, criar projetos e gerenciar recursos.

## Criar um projeto e hub de IA do Azure

Um hub IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto e hub de IA do Azure.

1. Na home page, selecione **+Criar projeto**. No assistente **Criar um projeto**, você pode ver todos os recursos do Azure que serão criados automaticamente com seu projeto ou pode personalizar as seguintes configurações selecionando **Personalizar** antes de selecionar **Criar**:
   
    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Criar um novo grupo de recursos com um nome exclusivo ou selecionar um já criado*
    - **Local**: Selecione **Ajude-me a escolher** e, em seguida, selecione **gpt-35-turbo** na janela Auxiliar de local e use a região recomendada\*
    - **Conecte os Serviços de IA do Azure ou do OpenAI do Azure**: *Selecione para criar um novo serviço de IA ou usar um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota em cenários em que você está compartilhando um locatário com outros usuários. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

1. Se você selecionou **Personalizar**, selecione **Avançar** e revise sua configuração.
1. Clique em **Criar** e aguarde a conclusão do processo.
   
    Após a criação do projeto e hub de IA do Azure, você deverá ver algo semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um hub de IA do Azure no portal do Azure AI Foundry.](./media/azure-ai-resource.png)

1. Abra uma nova guia do navegador (deixando a guia do Azure AI Foundry aberta) e navegue até o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true), entrando com suas credenciais do Azure, se solicitado.
1. Navegue até ao grupo de recursos onde criou o seu hub IA do Azure e veja os recursos Azure que foram criados.

    ![Captura de tela de um hub de IA do Azure e recursos relacionados no portal do Azure.](./media/azure-portal.png)

1. Retorne à guia do navegador do portal do Azure AI Foundry.
1. Veja cada uma das páginas no painel do lado esquerdo da página do seu hub IA do Azure e observe os artefatos que pode criar e gerir. Na página **Centro de gerenciamento**, você pode selecionar **Recursos conectados**, no hub ou no projeto, e observar que as conexões com o OpenAI do Azure e os serviços de IA já foram criadas.
1. Se você estiver na página Centro de gerenciamento, selecione **Ir para o projeto**.

## Implantar e testar um modelo

Você pode usar um projeto para criar soluções complexas de IA baseadas em modelos de IA generativos. Explorar completamente todas as opções de desenvolvimento disponíveis no portal do Azure AI Foundry está além do escopo deste exercício, mas veremos algumas maneiras básicas de trabalhar com modelos em um projeto.

1. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Na página **Modelos + pontos de extremidade**, na guia **Implantações de modelo**, clique em **+ Implantar modelo**.
1. Procure o modelo **gpt-35-turbo** na lista, selecione e confirme.
1. Crie uma nova implantação do modelo com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado
      
    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 5.000 TPM são suficientes para os dados usados neste exercício.

1. Após a implantação do modelo, na página de visão geral da implantação, selecione **Abrir no playground**.
1. Na página **Chat playground**, certifique-se de que a implantação do seu modelo esteja selecionada na seção **Implantação**.
1. Na janela de chat, insira uma consulta como *O que é IA?* e veja a resposta:

    ![Captura de tela do playground no portal do Azure AI Foundry.](./media/playground.png)

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente a [portal do Azure](https://portal.azure.com?azure-portal=true) em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

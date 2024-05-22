---
lab:
  title: Explorar o Estúdio de IA do Azure
---

# Explorar o Estúdio de IA do Azure

Neste exercício, você usará o Estúdio de IA do Azure para criar um projeto e explorar um modelo de IA generativo.

> **Observação**: O Estúdio de IA do Azure está em versão prévia no momento em que este artigo foi gravado e está em desenvolvimento ativo. Alguns elementos do serviço podem não ser exatamente como descritos, e alguns recursos podem não funcionar como esperado.

> Para concluir este exercício, sua assinatura do Azure deve ser aprovada para acesso ao serviço do OpenAI do Azure.

Este exercício levará aproximadamente **30** minutos.

## Abrir o Estúdio de IA do Azure

Vamos começar exibindo o Estúdio de IA do Azure.

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure. A página inicial do Estúdio de IA do Azure é semelhante à seguinte imagem:

    ![Captura de tela do Estúdio de IA do Azure.](./media/azure-ai-studio-home.png)

1. Examine as informações na página inicial e exiba cada uma das guias, observando as opções para explorar modelos e recursos, criar projetos e gerenciar recursos.

## Criar um Hub de IA do Azure

Você precisa de um Hub de IA do Azure na sua assinatura do Azure para hospedar projetos. É possível criar esse recurso ao criar um projeto ou provisioná-lo com antecedência (que é o que faremos neste exercício).

1. Na seção **Gerenciamento**, selecione **Todos os hubs**, e selecione **+ Novo hub**. Crie um novo hub com as seguintes configurações:
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

    Depois que o Hub de IA do Azure for criado, ele deverá ter uma aparência semelhante à da imagem a seguir:

    ![Captura de tela dos detalhes do Hub de IA do Azure no Estúdio de IA do Azure.](./media/azure-ai-resource.png)

1. Abra uma nova guia do navegador (deixando a guia do Estúdio de IA do Azure aberta) e navegue até o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true), entrando com suas credenciais do Azure, se solicitado.
1. Navegue até o grupo de recursos no qual você criou o Hub de IA do Azure e veja os recursos do Azure que foram criados.

    ![Captura de tela de um Hub de IA do Azure e recursos relacionados no portal do Azure.](./media/azure-portal.png)

1. Retorne à guia do navegador do Estúdio de IA do Azure.
1. Visualize cada uma das páginas no painel do lado esquerdo da página do seu Hub de IA do Azure e observe os artefatos que você pode criar e gerenciar. Na página **Conexões**, observe que já foi criada uma conexão com o recurso do OpenAI do Azure que você criou com o Hub de IA do Azure chamado **Default_AzureOpenAI**.

## Criar um projeto

Um Hub de IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto em seu Hub de IA do Azure.

1. No Estúdio de IA do Azure, na página **Compilar**, selecione **+ Novo projeto**. Em seguida, no assistente **Criar um novo projeto**, crie um projeto com as seguintes configurações:
    - **Nome do projeto**: *Um nome exclusivo para seu projeto*
    - **Hub**: *Seu Hub de AI*
1. Aguarde até que seu projeto seja criado. O resultado deve ser similar à imagem a seguir:

    ![Captura de tela de uma página de detalhes do projeto no Estúdio de IA do Azure.](./media/azure-ai-project.png)

1. Exiba as páginas no painel do lado esquerdo, expandindo cada seção, e observe as tarefas que você pode executar e os recursos que você pode gerenciar em um projeto.

## Implantar e testar um modelo

Você pode usar um projeto para criar soluções complexas de IA baseadas em modelos de IA generativos. Explorar completamente todas as opções de desenvolvimento disponíveis no Estúdio de IA do Azure está além do escopo deste exercício, mas veremos algumas maneiras básicas de trabalhar com modelos em um projeto.

1. No painel à esquerda do seu projeto, na seção **Componentes**, selecione a página **Implantações**.
1. Na página **Implantações**, selecione **+ Criar** e crie uma implantação de ponto de extremidade em tempo real.
1. Na lista **Selecionar um modelo**, selecione o modelo **gpt-35-turbo** e confirme a sua seleção. Em seguida, implante o modelo com as seguintes configurações:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Modelo**: gpt-35-turbo
    - **Versão do modelo**: *Selecione a versão padrão*
    - **Opções avançadas**:
        - **Filtro de conteúdo**: Padrão
        - **Tipo de implantação**: Padrão
        - **Limite de taxa de fichas por minuto (milhares)**: 5 mil

    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 5.000 TPM são suficientes para os dados usados neste exercício.

1. Depois que o modelo tiver sido implantado, no painel à esquerda, na seção **Ferramentas**, selecione a página **Playground**.
1. Na página **Playground**,certifique-se de que a implantação de modelo esteja selecionada na seção **Configuração**. Em seguida, na seção **Sessão de bate-papo**, insira uma consulta como *O que é IA?* e veja a resposta:

    ![Captura de tela do playground no Estúdio de IA do Azure.](./media/playground.png)

## Limpeza

Se tiver terminado de explorar o Estúdio de IA do Azure, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou abra novamente a [portal do Azure](https://portal.azure.com?azure-portal=true) em uma nova guia do navegador) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

---
lab:
  title: Preparar-se para um projeto de desenvolvimento de IA
  description: Aprenda a organizar recursos de nuvem em hubs e projetos para que os desenvolvedores estejam preparados para o sucesso ao criar soluções de IA.
---

# Preparar-se para um projeto de desenvolvimento de IA

Neste exercício, você usará o portal da Fábrica de IA do Azure para criar um projeto, pronto para criar uma solução de IA.

Este exercício levará aproximadamente **30** minutos.

> **Observação**: algumas das tecnologias usadas neste exercício estão em versão prévia ou em desenvolvimento ativo. Você pode observar algum comportamento, avisos ou erros inesperados.

## Abra o portal do Azure AI Foundry

Vamos começar entrando no portal da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir (feche o painel **Ajuda** se estiver aberto):

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

1. Revise as informações na home page.

## Criar um projeto

Um *projeto* da IA do Azure fornecerá um workspace colaborativo para desenvolvimento de IA. Vamos começar escolhendo um modelo com o qual queremos trabalhar e criando um projeto para usá-lo.

> **Observação**: os projetos da Fábrica de IA podem ser baseados em um recurso da *Fábrica de IA do Azure*, que fornece acesso a modelos de IA (incluindo o OpenAI do Azure), aos Serviços de IA do Azure e outros recursos para desenvolver agentes de IA e soluções de chat. Como alternativa, os projetos podem ser baseados em recursos do *hub de IA*, que incluem conexões com recursos do Azure para armazenamento seguro, computação e ferramentas especializadas. Os projetos baseados na Fábrica de IA do Azure são ótimos para desenvolvedores que desejam gerenciar recursos para o desenvolvimento de aplicativos de chat ou agente de IA. Os projetos baseados em hub de IA são mais adequados para equipes de desenvolvimento empresarial que trabalham em soluções de IA complexas.

1. Na home page, na seção **Explorar modelos e recursos**, pesquise pelo modelo `gpt-4o`, que usaremos em nosso projeto.
1. Nos resultados da pesquisa, selecione o modelo **gpt-4o** para ver os detalhes e, na parte superior da página do modelo, clique em **Usar este modelo**.
1. Quando solicitado a criar um projeto, insira um nome válido para o projeto e expanda **Opções avançadas**.
1. Selecione **Personalizar** e especifique as seguintes configurações para o seu projeto:
    - **Recurso da Fábrica de IA do Azure**: *um nome válido para o recurso da Fábrica de IA do Azure*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *criar ou selecionar um grupo de recursos*
    - **Região**: *Selecione qualquer **Local compatível com os Serviços de IA***\*

    > \* Alguns recursos da IA do Azure são restritos por cotas de modelo regional. Caso um limite de cota seja excedido posteriormente no exercício, é possível que você precise criar outro recurso em uma região diferente.

1. Clique em **Criar** e aguarde a criação do projeto, incluindo a implantação do modelo gpt-4 selecionado.
1. Quando o projeto for criado, o playground de chat abrirá automaticamente para que você possa testar o modelo:

    ![Captura de tela do playground de chat de um projeto da Fábrica de IA do Azure](./media/ai-foundry-chat-playground.png)

1. No painel de navegação à esquerda, selecione **Visão geral** para ver a página principal do projeto, que será assim:

    ![Captura de tela de uma página de visão geral do projeto da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

1. Na parte inferior do painel de navegação à esquerda, selecione **Centro de gerenciamento**. O centro de gerenciamento é onde você pode definir as configurações nos níveis do *recurso* e do *projeto*, ambos mostrados no painel de navegação.

    ![Captura de tela da página do Centro de gerenciamento no portal da Fábrica de IA do Azure.](./media/ai-foundry-management.png)

    O nível de *recurso* se refere ao recurso **Fábrica de IA do Azure** que foi criado para respaldar o seu projeto. Esse recurso inclui conexões com os Serviços de IA do Azure e os modelos da Fábrica de IA do Azure, além de fornecer um local central para gerenciar o acesso do usuário a projetos de desenvolvimento de IA.

    O nível do *projeto* se refere ao seu projeto individual, onde você pode adicionar e gerenciar recursos específicos do projeto.

1. No painel de navegação, na seção do recurso da Fábrica de IA do Azure, clique em **Visão geral** para visualizar os detalhes.
1. Clique no link para o **Grupo de recursos** associado ao recurso para abrir uma nova guia do navegador e navegue até o portal do Azure. Entre com suas credenciais do Azure, se solicitado.
1. Veja o grupo de recursos no portal do Azure para ver os recursos do Azure que foram criados para respaldar o recurso da Fábrica de IA do Azure e o seu projeto.

    ![Captura de tela de um recurso da Fábrica de IA do Azure e recursos do projeto no portal do Azure.](./media/azure-portal-resources.png)

    Os recursos foram criados na região que você selecionou ao criar o projeto.

1. Feche a guia do portal do Azure e retorne ao portal da Fábrica de IA do Azure.

## Revisar as conexões do projeto

O projeto e o recurso da Fábrica de IA do Azure ao qual ele pertence incluem conexões com recursos que você pode usar em aplicativos de IA.

1. Na página Centro de gerenciamento, no painel de navegação, em seu projeto, selecione **Ir para o recurso**.
1. Na página **Visão geral** do projeto, visualize a seção **Pontos de extremidade e chaves**, que contém pontos de extremidade e chaves de autorização que você pode usar no código do aplicativo para acessar:
    - O projeto da Fábrica de IA do Azure e todos os modelos implantados nele.
    - O OpenAI do Azure nos modelos da Fábrica de IA do Azure.
    - Serviços de IA do Azure

## Testar um modelo de IA generativa

Agora que você sabe um pouco sobre a configuração do projeto da Fábrica de IA do Azure, pode retornar ao playground de chat para explorar o modelo implantado.

1. No painel de navegação à esquerda do projeto,clique em **Playgrounds**. 
1. Abra o **Playground de chat** e confirme se a implantação do modelo **gpt-4o** está selecionada na seção **Implantação**.
1. No painel **Configuração**, na caixa **Dê instruções e contexto ao modelo**, digite as seguintes instruções:

    ```
   You are a history teacher who can answer questions about past events all around the world.
    ```

1. Aplique as alterações para atualizar a mensagem do sistema.
1. Na janela de chat, insira uma consulta como `What are the key events in the history of Scotland?` e visualize a resposta:

    ![Captura de tela do playground no portal do Azure AI Foundry.](./media/ai-foundry-playground.png)

## Resumo

Neste exercício, você explorou a Fábrica de IA do Azure e viu como criar e gerenciar projetos e seus recursos relacionados.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. No [portal do Azure](https://portal.azure.com), em `https://portal.azure.com`, visualize o conteúdo do grupo de recursos onde você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

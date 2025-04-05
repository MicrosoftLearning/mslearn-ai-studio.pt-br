---
lab:
  title: Desenvolver um aplicativo de IA generativa multimodal
  description: 'Saiba como usar a Fábrica de IA do Azure para criar um aplicativo de IA generativa que aceite entradas de texto, imagem e áudio.'
---

# Desenvolver um aplicativo de IA generativa multimodal

Neste exercício, você usará o modelo de IA generativa *Phi-4-multimodal-instruct* para gerar respostas para prompts que incluem texto, imagens e áudio. Você desenvolverá um aplicativo que fornece assistência de IA com produtos frescos em uma mercearia usando a Fábrica de IA do Azure e o serviço de inferência de modelo da IA do Azure.

Este exercício levará aproximadamente **30** minutos.

> **Observação**: este exercício é baseado em SDKs de pré-lançamento, que podem estar sujeitos a alterações. Quando necessário, usamos versões específicas de pacotes que podem não refletir as versões mais recentes disponíveis.

## Criar um projeto do Azure AI Foundry

Vamos começar criando um projeto da Fábrica de IA do Azure.

1. Em um navegador da Web, abra o [Portal da Fábrica de IA do Azure](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure. Feche todas as dicas ou painéis de início rápido abertos na primeira vez que você entrar e, se necessário, use o logotipo da **Fábrica de IA do Azure** no canto superior esquerdo para navegar até a home page, que é semelhante à imagem a seguir:

    ![Captura de tela do portal do Azure AI Foundry.](./media/ai-foundry-home.png)

2. Na home page, selecione **+Criar projeto**.
3. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, `my-ai-project`) e revise os recursos do Azure que serão criados automaticamente para dar suporte ao seu projeto.
4. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome exclusivo – por exemplo `my-ai-hub`*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *crie um novo grupo de recursos com um nome exclusivo (por exemplo, `my-ai-resources`) ou selecione um existente*
    - **Região**: selecione uma das seguintes regiões\*:
        - Leste dos EUA
        - Leste dos EUA 2
        - Centro-Norte dos EUA
        - Centro-Sul dos Estados Unidos
        - Suécia Central
        - Oeste dos EUA
        - Oeste dos EUA 3
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure:*** crie um novo recurso de Serviços de IA com um nome apropriado (por exemplo, `my-ai-services`) ou use um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* No momento em que este artigo foi escrito, o modelo *Phi-4-multimodal-instruct* da Microsoft que usaremos neste exercício está disponível nessas regiões. Você pode verificar a disponibilidade regional mais recente para modelos específicos na [documentação da Fábrica de IA do Azure](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). No caso de um limite de cota regional ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

5. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
6. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Implantar um modelo

Agora você já pode implantar um modelo *Phi-4-multimodal-instruct* para aceitar prompts multimodais.

1. Na barra de ferramentas no canto superior direito da página do projeto da Fábrica de IA do Azure, use o ícone **Recursos de visualização** para ativar o recurso **Implantar modelos para o serviço de inferência de modelo de IA do Azure**. Esse recurso garante que a implantação do modelo esteja disponível para o serviço de inferência de IA do Azure, que você usará no código do aplicativo.
2. No painel à esquerda do seu projeto, na seção **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
3. Na página **Modelos + pontos extremidades**, na guia **Implantações de modelo**, no menu **+ Implantar modelo**, selecione **Implantar modelo base**.
4. Procure o modelo **Phi-4-multimodal-instruct** na lista e, em seguida, selecione-o e confirme-o.
5. Concorde com o contrato de licença, se solicitado, e implante o modelo com as seguintes configurações selecionando **Personalizar** nos detalhes da implantação:
    - **Nome de implantação**: *um nome exclusivo para sua implantação de modelo – por exemplo `Phi-4-multimodal` (lembre-se do nome que você atribui, você precisará dele mais tarde*)
    - **Tipo de implantação**: padrão global
    - **Detalhes da implantação**: *use as configurações padrão*
6. Aguarde que o estado de provisionamento de implantação seja **Concluído**.

## Criar um aplicativo cliente

Agora que você implantou o modelo, pode usar a implantação em um aplicativo cliente.

> **Dica**: você pode optar por desenvolver sua solução usando Python ou Microsoft C# *(em breve)*. Siga as instruções na seção apropriada para o idioma escolhido.

### Preparar a configuração de aplicativo

1. No Portal da Fábrica de IA do Azure, visualize a página **Visão geral** do seu projeto.
2. Na área **Detalhes do projeto**, observe a **Cadeia de conexão do projeto**. Você usará essa cadeia de conexão para se conectar ao seu projeto em um aplicativo cliente.
3. Abra uma nova guia do navegador (mantendo o portal da Fábrica de IA do Azure aberto na guia existente). Em seguida, na nova guia, navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`; efetue login com suas credenciais do Azure, se solicitado.
4. Use o botão **[\>_]** à direita da barra de pesquisa na parte superior da página para criar um Cloud Shell no portal do Azure selecionando um ambiente do ***PowerShell***. O Cloud Shell fornece uma interface de linha de comando em um painel na parte inferior do portal do Azure.

    > **Observação**: se você já criou um Cloud Shell que usa um ambiente *Bash*, alterne-o para o ***PowerShell***.

5. Na barra de ferramentas do Cloud Shell, no menu **Configurações**, selecione **Ir para a versão clássica** (isso é necessário para usar o editor de código).

6. No painel do PowerShell, insira os seguintes comandos para clonar o repositório GitHub para este exercício:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Dica**: conforme você colar comandos no cloudshell, a saída pode ocupar uma grande quantidade do espaço da tela. Você pode limpar a tela digitando o comando `cls` para facilitar o foco em cada tarefa.

7. Após o repositório ser clonado, navegue até a pasta que contém os arquivos de código do aplicativo:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. No painel de linha de comando do Cloud Shell, digite o seguinte comando para instalar as bibliotecas que você usará:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
    ```

9. Digite o seguinte comando para editar o arquivo de configuração que foi fornecido:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    O arquivo é aberto em um editor de código.

10. No arquivo de código, substitua o espaço reservado **your_project_endpoint** pela cadeia de conexão do seu projeto (copiada da página **Visão Geral** do projeto no portal da Fábrica de IA do Azure) e o espaço reservado **your_model_deployment** pelo nome que você atribuiu à implantação do modelo Phi-4-multimodal-instruct.
11. Depois de substituir os espaços reservados, use o comando **CTRL+S** ou **botão direito do mouse > Salvar** para salvar as suas alterações e, em seguida, use o comando **CTRL+Q** ou **botão direito do mouse > Sair** para fechar o editor de código, mantendo a linha de comando do Cloud Shell aberta.

### Escrever código para se conectar ao projeto e obter um cliente de chat para o modelo

> **Dica**: ao adicionar código, certifique-se de manter o recuo correto.

1. Digite o seguinte comando para editar o arquivo de código que foi fornecido:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

2. No arquivo de código, observe as instruções existentes que foram adicionadas na parte superior do arquivo para importar os namespaces do SDK necessários. Em seguida, no comentário **Adicionar referências**, adicione o seguinte código para referenciar os namespaces nas bibliotecas que você instalou anteriormente:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. Na função **main**, no comentário **Get configuration settings**, observe que o código carrega a cadeia de conexão do projeto e os valores do nome de implantação do modelo que você definiu no arquivo de configuração.
4. No comentário **Initialize the project client**, adicione o seguinte código para se conectar ao seu projeto da Fábrica de IA do Azure usando as credenciais do Azure com as quais você está conectado no momento:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. No comentário **Get a chat client**, adicione o seguinte código para criar um objeto cliente para conversar com o modelo:

    **Python**

    ```
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Escrever código para usar um prompt baseado em texto

1. Observe que o código inclui um loop para permitir que o usuário insira um prompt até digitar "quit". Em seguida, na seção de loop, no comentário **Get a response to text input**, adicione o seguinte código para enviar um prompt baseado em texto e recuperar a resposta do modelo:

    **Python**

    ```python
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código; não feche-o ainda.

3. No painel da linha de comando do Cloud Shell abaixo do editor de código, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando solicitado, insira `1` para usar um prompt baseado em texto e, em seguida, insira o prompt `I want to make an apple pie. What kind of apple should I use?`
5. Revise a resposta. Em seguida, insira `quit` para sair do programa.

### Escrever código para usar um prompt baseado em imagem

1. No editor de código do arquivo **chat-app.py**, na seção de loop, no comentário **Get a response to image input**, adicione o seguinte código para enviar um prompt que inclua a seguinte imagem:

    ![Uma foto de uma laranja.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código; não feche-o ainda.

3. No painel da linha de comando do Cloud Shell abaixo do editor de código, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando solicitado, insira `2` para usar um prompt baseado em imagem e, em seguida, insira o prompt `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?`
5. Revise a resposta. Em seguida, insira `quit` para sair do programa.

### Escrever código para usar um prompt baseado em áudio

1. No editor de código do arquivo **chat-app.py**, na seção de loop, no comentário **Get a response to audio input**, adicione o seguinte código para enviar um prompt que inclua o seguinte áudio:

    <video controls src="./media/manzanas.mp4" title="O tempo é 2:15" width="150"></video>

    **Python**

    ```python
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```


2. Use o comando **CTRL+S** para salvar suas alterações no arquivo de código. Você também pode fechar o editor de código (**CTRL+Q**), se desejar.

3. No painel da linha de comando do Cloud Shell abaixo do editor de código, insira o seguinte comando para executar o aplicativo:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando solicitado, insira `3` para usar um prompt baseado em áudio e, em seguida, insira o prompt `What is this customer saying in English?`
5. Revise a resposta.
6. Você pode continuar executando o aplicativo, escolhendo diferentes tipos de prompt e testando diferentes prompts. Quando terminar, digite `quit` para sair do programa.

    Se você tiver tempo, pode modificar o código para usar um prompt do sistema diferente e seus próprios arquivos de imagem e áudio acessíveis pela Internet.

    > **Observação**: neste aplicativo simples, não implementamos lógica para reter o histórico de conversas; portanto, o modelo tratará cada prompt como uma nova solicitação sem contexto do prompt anterior.

## Resumo

Neste exercício, você usou a Fábrica de IA do Azure e o SDK de Inferência de IA do Azure para criar um aplicativo cliente que usa um modelo multimodal para gerar respostas para texto, imagens e áudio.

## Limpar

Se tiver terminado de explorar o portal da Fábrica de IA do Azure, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Retorne à guia do navegador que contém o portal do Azure (ou reabra o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com` em uma nova guia do navegador) e visualize o conteúdo do grupo de recursos onde você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

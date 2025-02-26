---
lab:
  title: Usar um prompt flow para gerenciar conversas em um aplicativo de chat
  description: Saiba como usar prompt flows para gerenciar diálogos de conversa e garantir que os prompts sejam construídos e orquestrados para obter melhores resultados.
---

# Usar um prompt flow para gerenciar conversas em um aplicativo de chat

Neste exercício, você usará o prompt flow do portal da Fábrica de IA do Azure para criar um aplicativo de chat personalizado que usa um prompt de usuário e um histórico de chat como entradas e usa um modelo GPT do OpenAI do Azure para gerar uma saída.

Este exercício levará aproximadamente **30** minutos.

## Criar um projeto e hub de IA no portal do Azure AI Foundry

Você deve começar criando um projeto do portal do Azure AI Foundry em um hub de IA do Azure:

1. Em um navegador da Web, abra [https://ai.azure.com](https://ai.azure.com) e entre usando suas credenciais do Azure.
1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, você pode ver todos os recursos do Azure que serão criados automaticamente com seu projeto ou pode personalizar as seguintes configurações selecionando **Personalizar** antes de selecionar **Criar**:

    - **Nome do hub**: *Um nome exclusivo*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *Um novo grupo de recursos*
    - **Localização**: selecione **Ajude-me a escolher** e então selecione **gpt-4** na janela do auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou a OpenAI do Azure**: (novo) *preenchido automaticamente com o nome do hub selecionado*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* Os recursos do OpenAI do Azure são restringidos no nível do locatário por cotas regionais. As regiões listadas no auxiliar de localização incluem a cota padrão para os tipos de modelos usados neste exercício. Escolher aleatoriamente uma região reduz o risco de uma única região atingir o seu limite de cota. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente. Saiba mais sobre a [disponibilidade do modelo por região](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#availability)

1. Se você selecionou **Personalizar**, selecione **Avançar** e revise sua configuração.
1. Clique em **Criar** e aguarde a conclusão do processo.

## Implantar um modelo GPT

Para usar um modelo de linguagem no prompt flow, você precisa primeiro implantar um modelo. O portal do Azure AI Foundry permite implantar modelos do OpenAI que você pode usar nos seus fluxos.

1. No painel de navegação à esquerda, em **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Selecione **+ Implantar modelo** e **Implantar modelo base**. 
1. Crie uma nova implantação do modelo **gpt-4** com as seguintes configurações selecionando **Personalizar** nos detalhes de implantação:
    - **Nome da implantação**: *Um nome exclusivo para sua implantação de modelo*
    - **Tipo de implantação**: Padrão
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA**: *escolha o recurso criado anteriormente*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
    - **Habilitar cota dinâmica**: Desabilitado

    > **Observação**: se o local atual do recurso de IA não tiver cota disponível para o modelo que você deseja implantar, será solicitado a escolher um local diferente onde um novo recurso de IA será criado e conectado ao seu projeto.

1. Aguarde até que o modelo seja implantado. Quando a implantação estiver pronta, selecione **Abrir no playground**.
1. Na janela de chat, insira a consulta `What can you do?`.

    Observe que a resposta é genérica porque não há instruções específicas para o assistente. Para torná-la focada em uma tarefa, você pode alterar o prompt do sistema.

1. Altere a mensagem **Dê instruções e contexto ao modelo** para o seguinte:

   ```md
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Selecione **Aplicar alterações**.
1. Na janela de chat, insira a mesma consulta de antes: `What can you do?` Observe a alteração na resposta.

Agora que você brincou com a mensagem do sistema para o modelo GPT implantado, você pode personalizar ainda mais o aplicativo trabalhando com o prompt flow.

## Criar e executar um fluxo de chat no portal do Azure AI Foundry

Você pode criar um novo fluxo a partir de um modelo ou criar um fluxo com base em suas configurações no playground. Como você já estava experimentando no playground, você usará esta opção para criar um novo fluxo.

<details>  
    <summary><b>Dica de solução de problemas</b>: erro de permissões</summary>
    <p>Se você receber um erro de permissões ao criar um novo prompt flow, tente o seguinte para solucionar o problema:</p>
    <ul>
        <li>No portal do Azure, selecione o recurso Serviços de IA.</li>
        <li>Na guia Identidade, em Gerenciamento de recursos, confirme se é uma identidade gerenciada atribuída pelo sistema.</li>
        <li>Navegue até a conta de armazenamento associada. Na página do IAM, adicione a atribuição de função <em>Leitor de Dados do Blob de Armazenamento</em>.</li>
        <li>Em <strong>Atribuir acesso a</strong>, escolha <strong>Identidade Gerenciada</strong>, <strong>+ Selecionar membros</strong>, selecione <strong>Todas as identidades gerenciadas atribuídas pelo sistema</strong> e selecione o recurso dos Serviços de IA do Azure.</li>
        <li>Revise e atribua para salvar as novas configurações e repita a etapa anterior.</li>
    </ul>
</details>

1. Em **Playground de chat**, selecione **Prompt flow** na barra superior.
1. Insira `Travel-Chat` como o nome da pasta.

    Um fluxo de chat simples é criado para você. Observe que há duas entradas (histórico de chat e a pergunta do usuário), um nó LLM que se conectará ao seu modelo de linguagem implantado e uma saída para refletir a resposta no chat.

    Para poder testar o seu fluxo, você precisa de computação.

1. Selecione **Iniciar sessão de computação** na barra superior.
1. A sessão de computação levará de 1 a 3 minutos para ser iniciada.
1. Localize o nó LLM chamado **chat**. Observe que o prompt já inclui o prompt do sistema especificado no playground de chat.

    Você ainda precisa conectar o nó LLM ao modelo implantado.

1. Na seção do nó LLM, em **Conexão**, selecione a conexão que foi criada para você quando você criou o hub de IA.
1. Para **API**, selecione **chat**.
1. Em **deployment_name**, selecione o modelo **gpt-4** implantado.
1. Para **response_format**, selecione **{"type":"text"}**.
1. Examine o campo de prompt e certifique-se de que ele se pareça com o seguinte:

   ```yml
   system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   ```

### Testar e implantar o fluxo

Agora que você desenvolveu o fluxo, você pode usar a janela de chat para testar o fluxo.

1. Certifique-se de que a sessão de computação esteja em execução.
1. Selecione **Salvar**.
1. Selecione **Chat** para testar o fluxo.
1. Insira a consulta: `I have one day in London, what should I do?` e examine a saída.

    Quando estiver satisfeito com o comportamento do fluxo criado, você poderá implantar o fluxo.

1. Selecione **Implantar** para implantar o fluxo com as seguintes configurações:
    - **Configurações básicas**:
        - **Ponto de extremidade**: Novo
        - **Nome do ponto de extremidade**: *Insira um nome exclusivo*
        - **Nome da implantação**: *Insira um nome exclusivo*
        - **Máquina virtual**: Standard_DS3_v2
        - **Contagem de instâncias**: 3
        - **Coleta de dados de inferência**: Habilitado
    - **Configurações avançadas**:
        - *Usar as configurações padrão*
1. No portal do Azure AI Foundry, em seu projeto, no painel de navegação à esquerda, em **Meus ativos**, selecione a página **Modelos + pontos de extremidade**.
1. Observe que, por padrão, as **Implantações de modelo** são listadas, incluindo o fluxo e o modelo de linguagem implantados. Pode levar algum tempo até que a implantação seja listada e criada com sucesso.
1. Quando a implantação for bem-sucedida, selecione-a. Em seguida, na página **Teste**, insira o prompt `What is there to do in San Francisco?` e revise a resposta.
1. Insira o prompt `Where else could I go?` e revise a resposta.
1. Exiba a página **Consumir** para o ponto de extremidade e observe que ele contém informações de conexão e código de exemplo que você pode usar para compilar um aplicativo cliente para o ponto de extremidade, permitindo que você integre a solução de prompt flow a um aplicativo como um copiloto personalizado.

## Excluir recursos do Azure

Quando terminar de explorar o portal do Azure AI Foundry, exclua os recursos criados para evitar custos desnecessários do Azure.

- Navegue até o [portal do Azure](https://portal.azure.com) em `https://portal.azure.com`.
- No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
- Selecione o grupo de recursos criado para este exercício.
- Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
- Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.

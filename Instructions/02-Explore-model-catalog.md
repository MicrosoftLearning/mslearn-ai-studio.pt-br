---
lab:
  title: Escolher e implantar um modelo de linguagem
  description: Os aplicativos de IA generativa são criados em um ou mais modelos de linguagem. Saiba como encontrar e selecionar modelos apropriados para seu projeto de IA generativa.
---

# Escolher e implantar um modelo de linguagem

O catálogo de modelos do Azure IA Foundry serve como um repositório central onde você pode explorar e usar uma variedade de modelos, facilitando a criação do cenário de IA generativa.

Neste exercício, você explorará o catálogo de modelos no portal da Fábrica de IA do Azure e comparará modelos potenciais para um aplicativo de IA generativa que auxilia na solução de problemas.

Este exercício levará aproximadamente **25** minutos.

## Criar um projeto e hub de IA do Azure

Um hub IA do Azure fornece um espaço de trabalho colaborativo no qual você pode definir um ou mais *projetos*. Vamos criar um projeto e hub de IA do Azure.

1. Em um navegador da Web, abra o [portal do Azure IA Foundry](https://ai.azure.com) em `https://ai.azure.com` e entre usando suas credenciais do Azure.

1. Na home page, selecione **+Criar projeto**.
1. No assistente **Criar um projeto**, insira um nome de projeto adequado (por exemplo, `my-ai-project`) e revise os recursos do Azure que serão criados automaticamente para dar suporte ao seu projeto.
1. Selecione **Personalizar** e especifique as seguintes configurações para o hub:
    - **Nome do hub**: *um nome exclusivo – por exemplo `my-ai-hub`*
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: *crie um novo grupo de recursos com um nome exclusivo (por exemplo, `my-ai-resources`) ou selecione um existente*
    - **Localização**: selecione **Ajude-me a escolher** e então selecione **gpt-4** na janela do auxiliar de localização e use a região recomendada\*
    - **Conectar os Serviços de IA do Azure ou o OpenAI do Azure:*** crie um novo recurso de Serviços de IA com um nome apropriado (por exemplo, `my-ai-services`) ou use um existente*
    - **Conectar-se à Pesquisa de IA do Azure**: Ignorar a conexão

    > \* As cotas do modelo são restritas no nível do locatário por cotas regionais. No caso de um limite de cota ser atingido mais adiante no exercício, há a possibilidade de você precisar criar outro recurso em uma região diferente.

1. Clique em **Avançar** e revise a configuração. Em seguida, selecione **Criar** e aguarde a conclusão do processo.
1. Quando o projeto for criado, feche todas as dicas exibidas e examine a página do projeto no Portal da Fábrica de IA do Azure, que deve ser semelhante à imagem a seguir:

    ![Captura de tela dos detalhes de um projeto IA do Azure no Portal da Fábrica de IA do Azure.](./media/ai-foundry-project.png)

## Configurar a implantação do serviço de inferência da IA do Azure

Há várias opções para implantar modelos no portal da Fábrica de IA do Azure. Neste exercício, você usará a opção de implantação **inferência de modelo da IA do Azure**, que dá suporte aos modelos do *OpenAI do Azure* e *modelo como serviço* do catálogo de modelos da Fábrica de IA do Azure. Como todos os modelos são implantados em um ponto de extremidade comum hospedado pelo recurso Serviços de IA do Azure, é fácil alternar entre os modelos ao testá-los para comparar o comportamento e o desempenho.

1. Na barra de ferramentas no canto superior direito da página do projeto da Fábrica de IA do Azure, use o ícone **Recursos de visualização** (📣) para exibir os recursos de visualização.
1. Verifique se o recurso **Implantar modelos no serviço de inferência de modelo da IA do Azure** está habilitado. Em seguida, feche o painel **Recursos de visualização**.

## Revisar detalhes e parâmetros do modelo

Para ajudar a escolher um modelo, você pode explorar descrições e parâmetros de modelo para determinar qual modelo melhor atende às suas necessidades.

1. No portal do projeto da Fábrica de IA do Azure, no painel de navegação à esquerda, selecione **Catálogo de modelos**.
1. Na home page do catálogo de modelos, pesquise `gpt-4` para encontrar o modelo de conclusão do chat **gpt-4**.

    ![Captura de tela de uma pesquisa por "gpt-4" no catálogo de modelos.](./media/model-catalog-search-gpt4.png)

1. Selecione o modelo **gpt-4** para ver os detalhes. Leia a descrição e revise as demais informações disponíveis na página.

    ![Captura de tela da página de detalhes do modelo gpt-4.](./media/gpt4-details.png)

1. Na página **gpt-4**, exiba a guia **Parâmetros** para ver como o modelo se compara em alguns parâmetros de desempenho padrão com outros modelos usados em cenários semelhantes.

    ![Captura de tela da página do modelo de parâmetro de comparação gpt-4.](./media/gpt4-benchmarks.png)

1. Use a seta de voltar (**&larr;**) ao lado do título da página **gpt-4** para retornar à home page do catálogo de modelos.
1. No catálogo de modelos, pesquise `Phi-3.5-mini-instruct` e visualize os detalhes e parâmetros do modelo **Phi-3.5-mini-instruct**.

## Comparar modelos

Você analisou dois modelos diferentes, ambos os quais poderiam ser usados para implementar um aplicativo de chat de IA generativa. Agora vamos comparar as métricas desses dois modelos visualmente.

1. Retorne à home page do **Catálogo de modelos**.
1. Selecione **Comparar modelos**. Um gráfico visual para comparação de modelos é exibido com uma seleção de modelos comuns.

    ![Captura de tela da página de comparação de modelos.](./media/compare-models.png)

1. No painel **Modelos a serem comparados** à esquerda, você pode selecionar tarefas populares, como *respostas às perguntas*, para selecionar automaticamente modelos comumente usados para tarefas específicas.
1. Use o ícone **Limpar todos os modelos** (&#128465;) para remover todos os modelos pré-selecionados.
1. Use o botão **+ Modelo a ser comparado** para adicionar o modelo **gpt-4** à lista. Em seguida, use o mesmo botão para adicionar o modelo **Phi-3.5-mini-instruct** à lista.
1. Examine o gráfico, que compara os modelos com base no **Índice de qualidade** (uma pontuação padronizada que indica a qualidade do modelo) e no **Custo**. Você pode ver os valores específicos de um modelo mantendo o mouse sobre o ponto que o representa no gráfico.

    ![Captura de tela do gráfico de comparação de modelos dos modelos gpt-4 e Phi-3.5-mini-instruct.](./media/comparison-chart.png)

1. No menu suspenso **Eixo X**, em **Qualidade**, selecione as seguintes métricas e observe cada gráfico resultante antes de passar para o próximo:
    - Precisão
    - Coerência
    - Fluência
    - Relevância

## Implantar modelos

Agora que explorou suas opções usando parâmetros de comparação de modelo, você já pode implantar modelos de linguagem. Você pode navegar pelo catálogo de modelos e implantar a partir daí ou pode implantar um modelo na página **Implantações**. Vamos explorar as duas opções.

### Implantar um modelo no *Catálogo de modelos*

Vamos começar implantando um modelo do catálogo de modelos. Você pode preferir essa opção quando quiser revisar diversos modelos disponíveis.

1. Retorne à home page do **Catálogo de modelos**.
1. Pesquise e selecione o modelo `gpt-4`, assim como você fez anteriormente.
1. Na página **gpt-4**, selecione **Implantar** e implante o modelo com as seguintes configurações selecionando **Personalizar** nos detalhes da implantação:
    - **Nome da implantação**: *um nome exclusivo para a implantação do seu modelo — por exemplo `gpt-4-model`*
    - **Tipo de implantação**: padrão global
    - **Versão do modelo**: *selecione a versão padrão*
    - **Recurso de IA conectado**: *sua conexão de recursos do OpenAI do Azure*
    - **Limite de taxa de fichas por minuto (milhares)**: 5 mil
    - **Filtro de conteúdo**: DefaultV2
      
    > **Observação**: A redução do TPM ajuda a evitar o uso excessivo da cota disponível na assinatura que você está usando. 5.000 TPM são suficientes para os dados usados neste exercício.

1. Aguarde até que o **Estado de provisionamento** da implantação seja **Concluído**.

### Implantar um modelo por meio de *Modelos + pontos de extremidade*

Se você já sabe exatamente qual modelo quer implantar, talvez prefira fazê-lo por meio do **Modelos + pontos de extremidade**.

1. Na barra de navegação à esquerda, na seção **Meus ativos**, selecione **Modelos + pontos de extremidade**.
1. Na guia **Implantações de modelo**, na lista suspensa **+ Implantar modelo**, selecione **Implantar modelo base**. Pesquise por `Phi-3.5-mini-instruct` e confirme a selação.
1. Concorde com a licença do modelo.
1. Implante um modelo **Phi-3.5-mini-instruct** com as seguintes configurações:
    - **Nome da implantação**: *um nome exclusivo para a implantação do seu modelo — por exemplo, `phi-35-model`*
    - **Tipo de implantação**: padrão global
    - **Detalhes da implantação**: *use as configurações padrão*

1. Aguarde até que o **Estado de provisionamento** da implantação seja **Concluído**.

## Testar modelos no playground de chat

Agora que você tem dois modelos para comparar, vamos ver como os modelos se comportam em uma interação de conversa.

### Preparar-se para o chat

1. Na barra de navegação, selecione **Playgrounds**. Depois, selecione o **Playground Chat**.
1. No painel **Configuração**, no campo **Fornecer instruções e contexto ao modelo**, defina o prompt do sistema como `You are an AI assistant that helps solve problems.`
1. Selecione **Aplicar alterações**.

### Converse com o modelo *gpt-4*

No painel **Configuração**, selecione o modelo *gpt-4*.
1. Na janela de chat, insira a seguinte consulta

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Exiba a resposta. Depois, insira a consulta de acompanhamento a seguir:

    ```
    Explain your reasoning.
    ```

### Converse com o modelo *Phi-3.5*

1. No painel **Configuração**, selecione o modelo *Phi-3.5*.
1. Certifique-se de que uma nova sessão de chat seja iniciada antes de repetir os mesmos prompts usados anteriormente para testar o modelo gpt-4.
1. Na janela de chat, insira a seguinte consulta

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Exiba a resposta. Depois, insira a consulta de acompanhamento a seguir:

    ```
    Explain your reasoning.
    ```

### Faça uma comparação adicional

1. Experimente o seguinte quebra-cabeça com ambos os modelos, pedindo aos modelos que expliquem seu raciocínio (a resposta correta é 40!):

    ```
    I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Reflita sobre os modelos

Você comparou dois modelos, que podem variar em termos de capacidade de gerar respostas apropriadas e de custo. Em qualquer cenário generativo, você precisa encontrar um modelo com o equilíbrio certo de adequação para a tarefa que você precisa executar e o custo de usar o modelo para o número de solicitações que você espera que ele tenha que processar.

Os detalhes e parâmetros fornecidos no catálogo de modelos, além da capacidade de comparar modelos visualmente, fornecem um ponto de partida útil ao identificar modelos candidatos para uma solução de IA generativa. Em seguida, você pode testar modelos candidatos com uma variedade de prompts de sistema e usuário no playground de chat.

## Limpar

Se tiver terminado de explorar o portal do Azure AI Foundry, deverá excluir os recursos que criou neste exercício para evitar incorrer em custos desnecessários do Azure.

1. Abra o [portal do Azure](https://portal.azure.com) e exiba o conteúdo do grupo de recursos em que você implantou os recursos usados neste exercício.
1. Na barra de ferramentas, selecione **Excluir grupo de recursos**.
1. Insira o nome do grupo de recursos e confirme que deseja excluí-lo.

# azure_cognitive_search

## Este projeto visa criar uma solução de busca e enriquecimento de dados utilizando recursos da Azure. A solução envolve a configuração de três principais recursos:

### Azure AI Search:

Função: Gerenciar a indexação e consulta de dados.

Configurações:

Subscription: Sua assinatura Azure.

Resource group: Grupo de recursos único.

Service name: Nome único para o serviço.

Location: Região disponível (ex: "East US 2").

Pricing tier: Básico.

Azure AI Services:

Função: Fornecer serviços de IA para enriquecer os dados com insights gerados por IA.

Configurações:

Subscription: Mesma assinatura do Azure AI Search.

Resource group: Mesmo grupo de recursos do Azure AI Search.

Region: Mesma localização do Azure AI Search.

Name: Nome único para o serviço.

Pricing tier: Standard S0.

Storage Account:

Função: Armazenar documentos brutos e outras coleções de tabelas, objetos ou arquivos.

Configurações:

Subscription: Mesma assinatura dos recursos anteriores.

Resource group: Mesmo grupo de recursos dos recursos anteriores.

Storage account name: Nome único para a conta de armazenamento.

Location: Qualquer local disponível.

Performance: Standard.

Redundancy: Armazenamento com redundância local (LRS).

Configuração adicional: Habilitar o acesso anônimo para blobs.

Passos para Implementação
Criar o recurso Azure AI Search:

Acesse o portal Azure, crie o recurso com as configurações especificadas e aguarde a implantação.

Criar o recurso Azure AI Services:

Retorne à página inicial do portal Azure, crie o recurso com as configurações especificadas, garantindo que esteja na mesma localização do Azure AI Search.

Criar a Storage Account:

Crie a conta de armazenamento com as configurações especificadas e habilite o acesso anônimo para blobs.

### Observação Importante

#### Localização: Os recursos Azure AI Search e Azure AI Services devem estar na mesma localização para funcionarem corretamente.

Upload de Documentos para o Azure Storage

Criar um Contêiner no Azure Storage:

No portal Azure, acesse a conta de armazenamento criada anteriormente.

No menu lateral, selecione Containers.

Clique em + Container e configure com os seguintes valores:

Name: coffee-reviews

Public access level: Container (acesso de leitura anônimo para contêineres e blobs).

Clique em Create.

Fazer Upload dos Documentos:

Faça o download do arquivo ZIP com as avaliações de café em: https://aka.ms/mslearn-coffee-reviews.

Extraia os arquivos para uma pasta local chamada reviews.

No portal Azure, selecione o contêiner coffee-reviews e clique em Upload.

No painel Upload blob, selecione todos os arquivos da pasta reviews e clique em Upload.

Após o upload, os documentos estarão disponíveis no contêiner coffee-reviews.

Indexação dos Documentos no Azure AI Search

Iniciar o Assistente de Importação de Dados:

No portal Azure, acesse o recurso Azure AI Search criado anteriormente.

Na página Overview, clique em Import data para iniciar o assistente.

Configurar a Fonte de Dados:

Na página Connect to your data, selecione Azure Blob Storage como fonte de dados.

Configure os seguintes valores:

Data source name: coffee-customer-data

Data to extract: Content and metadata

Parsing mode: Default

Connection string: Selecione a conta de armazenamento e o contêiner coffee-reviews.

Description: Reviews for Fourth Coffee shops.

Clique em Next: Add cognitive skills (Optional).

Adicionar Habilidades Cognitivas (Cognitive Skills):

Na seção Attach AI Services, selecione o recurso Azure AI services criado anteriormente.

Na seção Add enrichments:

Altere o nome do Skillset para coffee-skillset.

Marque a opção Enable OCR e selecione Merge all text into merged_content field.

Defina o Source data field como merged_content.

Defina o Enrichment granularity level como Pages (5000 character chunks).

Selecione os seguintes campos enriquecidos:

Extract location names → locations

Extract key phrases → keyphrases

Detect sentiment → sentiment

Generate tags from images → imageTags

Generate captions from images → imageCaption

Na seção Save enrichments to a knowledge store:

Selecione as opções de projeções: Image projections, Documents, Pages, Key phrases, Entities, Image details, Image references.

Quando solicitado, selecione a conta de armazenamento criada anteriormente e crie um novo contêiner chamado knowledge-store com o nível de privacidade Private.

Personalizar o Índice:

Na página Customize target index, defina o nome do índice como coffee-index.

Defina o campo Key como metadata_storage_path.

Marque como Filterable os seguintes campos: content, locations, keyphrases, sentiment, merged_content, text, layoutText, imageTags, imageCaption.

Clique em Next: Create an indexer.

Criar o Indexador:

Defina o nome do indexador como coffee-indexer.

Deixe o agendamento como Once.

Nas Advanced options, marque a opção Base-64 Encode Keys.

Clique em Submit para criar a fonte de dados, skillset, índice e indexador.

Executar o Indexador:

Após a criação, o indexador será executado automaticamente.

Acesse o recurso Azure AI Search, vá para Indexers e verifique o status do coffee-indexer.

Aguarde até que o status indique Success.

Resultado Final
Os documentos foram carregados no contêiner coffee-reviews do Azure Storage.

Os dados foram indexados e enriquecidos com insights gerados por IA no índice coffee-index do Azure AI Search.

O indexador coffee-indexer foi configurado para processar os documentos e gerar campos enriquecidos, como localizações, frases-chave, sentimentos e descrições de imagens.

Continuando o Readme: Consultando o Índice e Revisando o Knowledge Store
Consultando o Índice com o Search Explorer
O Search Explorer é uma ferramenta integrada ao portal Azure que permite testar consultas e validar a qualidade do índice de busca. Aqui está como utilizá-lo:

Acessar o Search Explorer:

No portal Azure, acesse o recurso Azure AI Search.

Na página Overview, clique em Search explorer no topo da tela.

Certifique-se de que o índice selecionado seja o coffee-index criado anteriormente.

Altere a visualização para JSON view.

Executar Consultas:

Consulta 1: Retornar todos os documentos:

No editor de consultas JSON, insira:

    {
        "search": "*",
        "count": true
    }
    
Clique em Search. A consulta retornará todos os documentos no índice, com a contagem total no campo @odata.count.

Consulta 2: Filtrar por localização (Chicago):

No editor de consultas JSON, insira:

    {
        "search": "locations:'Chicago'",
        "count": true
    }
    
Clique em Search. A consulta retornará apenas os documentos que mencionam a localização "Chicago". O campo @odata.count deve mostrar 3.

Consulta 3: Filtrar por sentimento (negativo):

No editor de consultas JSON, insira:

    {
        "search": "sentiment:'negative'",
        "count": true
    }
    
Clique em Search. A consulta retornará apenas os documentos com sentimento negativo. O campo @odata.count deve mostrar 1.

Observação: Os resultados são classificados por @search.score, que indica a relevância dos documentos em relação à consulta.

Analisar Frases-Chave:

Para entender o motivo de uma avaliação negativa, revise as frases-chave associadas ao documento com sentimento negativo. Isso pode fornecer insights sobre possíveis problemas.

Revisando o Knowledge Store
O Knowledge Store armazena os dados enriquecidos gerados pelas habilidades cognitivas (cognitive skills) em projeções e tabelas. Aqui está como explorá-lo:

Acessar o Knowledge Store:

No portal Azure, navegue até a conta de armazenamento criada anteriormente.

No menu lateral, selecione Containers e escolha o contêiner knowledge-store.

Explorar as Projeções:

Dentro do contêiner knowledge-store, você verá pastas correspondentes a cada documento processado.

Selecione uma pasta e abra o arquivo objectprojection.json para visualizar os dados enriquecidos em formato JSON.

Clique em Edit para ver os detalhes do JSON gerado para um documento específico.

Visualizar Imagens Armazenadas:

Retorne ao contêiner coffee-skillset-image-projection.

Selecione qualquer arquivo .jpg para visualizar as imagens extraídas dos documentos.

Clique em Edit para ver a imagem armazenada.

Explorar Tabelas de Frases-Chave:

No menu lateral, selecione Storage browser e depois Tables.

Escolha a tabela coffeeSkillsetKeyPhrases para ver as frases-chave extraídas das avaliações.

Observe como as tabelas podem ser relacionadas, semelhante a um banco de dados relacional, com campos-chave que permitem vincular informações.

Conclusão
Search Explorer: Permite testar consultas e validar os resultados do índice coffee-index, filtrando por localização, sentimento e outros campos enriquecidos.

Knowledge Store: Armazena dados enriquecidos, como frases-chave, sentimentos e imagens, em um formato estruturado que pode ser explorado para análises mais profundas.


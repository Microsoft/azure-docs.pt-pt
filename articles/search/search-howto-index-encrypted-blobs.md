---
title: Pesquisa sobre o conteúdo de armazenamento encriptado Azure Blob
titleSuffix: Azure Cognitive Search
description: Saiba como indexar e extrair texto de documentos encriptados no Azure Blob Storage com Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96530940"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Como indexar bolhas encriptadas usando indexadores blob e skillsets em Azure Cognitive Search

Este artigo mostra-lhe como usar [a Azure Cognitive Search](search-what-is-azure-search.md) para indexar documentos que foram previamente encriptados dentro do [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) usando o Cofre da Chave [Azure](../key-vault/general/overview.md). Normalmente, um indexante não pode extrair conteúdo de ficheiros encriptados porque não tem acesso à chave de encriptação. No entanto, aproveitando a habilidade personalizada [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) seguida do [DocumentExtractionSkill,](cognitive-search-skill-document-extraction.md)pode fornecer acesso controlado à chave para desencriptar os ficheiros e, em seguida, obter conteúdo extraído dos mesmos. Isto desbloqueia a capacidade de indexar estes documentos sem comprometer o estado de encriptação dos seus documentos armazenados.

Começando com documentos inteiros previamente encriptados (texto não estruturado) como PDF, HTML, DOCX e PPTX no armazenamento Azure Blob, este guia utiliza o Carteiro e as APIs de Search REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Defina um oleoduto que desencripta os documentos e extrai texto dos mesmos.
> * Defina um índice para armazenar a saída.
> * Execute o oleoduto para criar e carregar o índice.
> * Explore resultados utilizando a pesquisa completa por texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo pressupõe que já fez o upload dos seus ficheiros para o Azure Blob Storage e os encriptaram no processo. Se precisar de ajuda para obter os seus ficheiros inicialmente carregados e encriptados, consulte [este tutorial](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) para saber como fazê-lo.

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) na mesma subscrição que a Azure Cognitive Search. O cofre da chave deve ter **proteção de eliminação** e **purga** ativada.
+ [Pesquisa Cognitiva Azure](search-create-service-portal.md) num [nível de faturação](search-sku-tier.md#tier-descriptions) (Básico ou superior, em qualquer região)
+ [Função do Azure](https://azure.microsoft.com/services/functions/)
+ [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 - Criar serviços e recolher credenciais

### <a name="set-up-the-custom-skill"></a>Configurar a habilidade personalizada

Este exemplo utiliza o projeto [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) da amostra Do repositório [GitHub.](https://github.com/Azure-Samples/azure-search-power-skills) Nesta secção, irá utilizar a habilidade para uma Função Azure para que possa ser utilizada numa habilidade. Um script incorporado cria um recurso Azure Function nomeado começando com **a aplicação psdbf-function-** e carrega a habilidade. Será solicitado para fornecer um grupo de subscrição e recursos. Não se esqueça de escolher a mesma subscrição em que o seu exemplo de Azure Key Vault vive.

Operacionalmente, a habilidade DecryptBlobFile leva o símbolo DE URL e SAS para cada bolha como entradas, e produz o ficheiro descarregado e desencriptado usando o contrato de referência de ficheiro que a Azure Cognitive Search espera. Lembre-se que o DecryptBlobFile precisa da chave de encriptação para executar a desencriptação. Como parte da configuração, também irá criar uma política de acesso que concede acesso à função DecryptBlobFile à chave de encriptação no Cofre da Chave Azure.

1. Clique no botão **Implementar para Azure** encontrado na [página de aterragem DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), que abrirá o modelo de Gestor de Recursos fornecido dentro do portal Azure.

1. Selecione **a subscrição onde existe a sua instância Azure Key Vault** (este guia não funcionará se selecionar uma subscrição diferente) e selecionar um grupo de recursos existente ou criar um novo (se criar um novo, também terá de selecionar uma região para implementar).

1. Selecione **Rever + criar,** certifique-se de que concorda com os termos e, em seguida, selecione **Criar** para implementar a Função Azure.

    ![Modelo de ARM no portal](media/indexing-encrypted-blob-files/arm-template.jpg "Modelo de ARM no portal")

1. Aguarde pela conclusão da implementação.

1. Navegue para o seu Azure Key Vault no portal. [Crie uma política de acesso](../key-vault/general/assign-access-policy-portal.md) no Cofre da Chave Azure que concede acesso chave à habilidade personalizada.
 
    1. Em **Definições**, selecione **políticas de acesso** e, em seguida, selecione Adicionar a política de **acesso**
     
       ![Keyvault adicionar política de acesso](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Políticas de acesso keyvault")

    1. Sob **configuração do modelo,** selecione **Azure Data Lake Storage ou Azure Storage**.

    1. Para o principal, selecione a instância Azure Function que implementou. Pode pesquisar através do prefixo de recursos que foi usado para criá-lo no passo 2, que tem um valor prefixo prefixo prefixo padrão da **aplicação psdbf-function.**

    1. Não selecione nada para a opção de aplicação autorizada.
     
        ![Keyvault adicionar modelo de política de acesso](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Modelo de política de acesso keyvault")

    1. Certifique-se de clicar em **Guardar** na página de políticas de acesso antes de navegar para adicionar a política de acesso.
     
         ![Keyvault salvar a política de acesso](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Salvar a política de acesso keyvault")

1. Navegue para a função **psdbf-function-app** no portal e tome nota das seguintes propriedades, uma vez que necessitará delas mais tarde no guia:

    1. O URL de função, que pode ser encontrado no **Essencial** na página principal para a função.
    
        ![URL da Função](media/indexing-encrypted-blob-files/function-uri.jpg "Onde encontrar o URL da Função Azure")

    1. O código chave do anfitrião, que pode ser encontrado navegando nas **teclas app,** clicando para mostrar a chave **predefinida** e copiando o valor.
     
        ![Código chave do anfitrião da função](media/indexing-encrypted-blob-files/function-host-key.jpg "Onde encontrar o código-chave do anfitrião da Função Azure")

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA e a execução de skillset são apoiados pelos Serviços Cognitivos, incluindo Análise de Texto e Visão Computacional para o processamento de linguagem natural e imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, você iria neste momento providenciar Serviços Cognitivos (na mesma região que a Azure Cognitive Search) para que você possa anexá-lo a operações de indexação.

Para este exercício, no entanto, você pode ignorar o fornecimento de recursos porque a Azure Cognitive Search pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por indexer executado. Depois de processar 20 documentos, o indexante falhará a menos que uma chave de Serviços Cognitivos esteja ligada ao skillset. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para obter mais informações, consulte [Os Serviços Cognitivos anexados.](cognitive-search-attach-cognitive-services.md) Note que uma chave de Serviços Cognitivos é necessária para executar um skillset com mais de 20 documentos, mesmo que nenhuma das suas habilidades cognitivas selecionadas se conecte aos Serviços Cognitivos (como com o skillset fornecido se não forem adicionadas competências a ele).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O último componente é a Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Livre para completar este guia. 

Tal como acontece com a Função Azure, desembrose um momento para recolher a chave de administração. Mais à frente, quando iniciar os pedidos estruturantes, terá de fornecer o ponto final e a tecla api de administração utilizada para autenticar cada pedido.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **de Visão Geral** do seu serviço de pesquisa, obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL de ponto final `https://mydemo.search.windows.net` fosse, o seu nome de serviço seria `mydemo` .

2. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-javascript/service-name-and-keys.png)

Todos os pedidos requerem uma chave API no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido de envio do pedido e o serviço que o trata.

## <a name="2---set-up-postman"></a>2 - Configurar carteiro

Instale e instale o Carteiro.

### <a name="download-and-install-postman"></a>Descarregue e instale o Carteiro

1. Descarregue o [código fonte de recolha do Carteiro.](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)
1. Selecione **a Importação de**  >  **Ficheiros** para importar o código fonte para o Carteiro.
1. Selecione o separador **Coleções** e, em seguida, selecione o botão **...** (elipse).
1. Selecione **Editar**. 
   
   ![Aplicativo de carteiro mostrando navegação](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Ir ao menu Editar no Carteiro")
1. Na caixa de diálogo **Editar,** selecione o **separador Variáveis.** 

No separador **Variáveis,** pode adicionar valores que o Carteiro troca sempre que encontra uma variável específica dentro de aparelhos duplos. Por exemplo, o Carteiro substitui o símbolo `{{admin-key}}` pelo valor atual que definiu para `admin-key` . O carteiro faz a substituição em URLs, cabeçalhos, o corpo de pedido, e assim por diante. 

Para obter o valor `admin-key` para , use a chave api-api de pesquisa cognitiva Azure que observou anteriormente. `search-service-name`Desacuça o nome do serviço de Pesquisa Cognitiva Azure que está a utilizar. Descreva `storage-connection-string` utilizando o valor no separador Chaves de **Acesso** da sua conta de armazenamento e definido para o nome do `storage-container-name` recipiente blob na conta de armazenamento onde os ficheiros encriptados estão armazenados. Desa cotado `function-uri` para o URL de função Azure que anotado anteriormente e desa cotado para o código de teclado do `function-code` anfitrião Azure Function que já observou. Pode deixar os padrão para os outros valores.

![Separador de variáveis de aplicativo do carteiro](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Janela de variáveis do carteiro")

| Variável    | Onde obtê-lo |
|-------------|-----------------|
| `admin-key` | Na página **Chaves** do serviço de Pesquisa Cognitiva Azure.  |
| `search-service-name` | O nome do serviço de Pesquisa Cognitiva Azure. A URL `https://{{search-service-name}}.search.windows.net` é. |
| `storage-connection-string` | Na conta de armazenamento, no separador **Chaves de Acesso,** selecione **a tecla1** Connection  >  **string**. |
| `storage-container-name` | O nome do recipiente blob que tem os ficheiros encriptados a serem indexados. |
| `function-uri` |  Na Função Azure sob **Essencial** na página principal. |
| `function-code` | Na Função Azure, navegando nas **teclas App,** clicando para mostrar a chave **predefinida** e copiando o valor. |
| `api-version` | Sair como **2020-06-30**. |
| `datasource-name` | Deixe como **blobs-ds encriptados.** |
| `index-name` | Deixe como **idx criptografado.** |
| `skillset-name` | Deixe como **blobs-ss encriptados.** |
| `indexer-name` | Deixe como **ixr criptografado.** |

### <a name="review-the-request-collection-in-postman"></a>Reveja a recolha de pedidos no Carteiro

Ao executar este guia, deve emitir quatro pedidos HTTP:

- **Pedido DE PUT para criar o índice**: Este índice contém os dados que a Azure Cognitive Search utiliza e devolve.
- **Pedido de PUBLICAÇÃO para criar a fonte de dados**: Esta fonte de dados liga o seu serviço de Pesquisa Cognitiva Azure à sua conta de armazenamento e, portanto, ficheiros blob encriptados. 
- **Pedido de CRIAÇÃO do skillset**: O skillset especifica a definição de habilidade personalizada para a Função Azure que irá desencriptar os dados do ficheiro blob, e um [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) para extrair o texto de cada documento depois de ter sido desencriptado.
- **PUT pedido para criar o indexante**: Executar o indexante lê os dados, aplica o skillset e armazena os resultados. Tem de fazer este pedido por último.

O [código fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) contém uma coleção do Carteiro que tem os quatro pedidos, bem como alguns pedidos úteis de acompanhamento. Para emitir os pedidos, no Carteiro, selecione o separador para os pedidos e selecione **Enviar** por cada um deles.

## <a name="3---monitor-indexing"></a>3 - Monitorização da indexação

A indexação e enriquecimento começam assim que submeter o pedido de 'Criar Indexer'. Dependendo de quantos documentos estão na sua conta de armazenamento, a indexação pode demorar algum tempo. Para saber se o indexante ainda está em funcionamento, utilize o pedido de Estado do **Indexante Fornecido** como parte da coleção do Carteiro e reveja a resposta para saber se o indexante está em execução ou para ver informações de erro e aviso.  

Se estiver a utilizar o nível Livre, espera-se a seguinte mensagem: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Esta mensagem aparece porque a indexação de blob no nível Livre tem um [limite de 32K na extração de caracteres](search-limits-quotas-capacity.md#indexer-limits). Não verá esta mensagem para este conjunto de dados em níveis mais altos. 

## <a name="4---search"></a>4 - Pesquisar

Após a execução do indexante estar concluída, pode executar algumas consultas para verificar se os dados foram desencriptados e indexados com sucesso. Navegue para o seu serviço de Pesquisa Cognitiva Azure no portal e use o [explorador de pesquisa](search-explorer.md) para executar consultas sobre os dados indexados.

## <a name="next-steps"></a>Passos seguintes

Agora que indexou com sucesso ficheiros encriptados, pode [iterar neste pipeline adicionando mais habilidades cognitivas](cognitive-search-defining-skillset.md). Isto permitir-lhe-á enriquecer e obter informações adicionais sobre os seus dados.

Se estiver a trabalhar com dados duplamente encriptados, talvez queira investigar as funcionalidades de encriptação do índice disponíveis na Pesquisa Cognitiva Azure. Embora o indexante precise de dados desencriptados para efeitos de indexação, uma vez que o índice existe, pode ser encriptado usando uma chave gerida pelo cliente. Isto garantirá que os seus dados são sempre encriptados quando em repouso. Para obter mais informações, consulte [as chaves geridas pelo cliente para encriptação de dados em Azure Cognitive Search](search-security-manage-encryption-keys.md).
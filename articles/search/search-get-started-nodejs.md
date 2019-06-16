---
title: Introdução ao Azure Search no node. js - Azure Search
description: Instruções sobre a compilação de uma aplicação de pesquisa num serviço de pesquisa na cloud alojado no Azure utilizando Node.js como linguagem de programação.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 1b37b3c52abd3750c3452a46bdf5b0c5954de4dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61289208"
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Introdução à Azure Search no Node.js
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Saiba como compilar uma aplicação de pesquisa Node.js personalizada que utiliza a Azure Search pela sua experiência de pesquisa. Este tutorial utiliza a [API REST do Serviço da Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações utilizados neste exercício.

Utilizamos [Node.js](https://Nodejs.org) e NPM, [Sublime Text 3](https://www.sublimetext.com/3) e Windows PowerShell no Windows 8.1 para desenvolver e testar este código.

Para executar este exemplo, precisa de um serviço da Azure Search e, para isso, pode inscrever-se no [portal do Azure](https://portal.azure.com). Consulte [Criar um serviço da Azure Search no portal](search-create-service-portal.md) para obter instruções passo a passo.

## <a name="about-the-data"></a>Sobre os dados
Esta aplicação de exemplo utiliza dados dos [Serviços Geológicos dos Estados Unidos (USGS)](https://geonames.usgs.gov/domestic/download_data.htm) filtrados no estado de Rhode Island de forma a reduzir o tamanho do conjunto de dados. Utilizaremos estes dados para compilar uma aplicação de pesquisa que devolve edifícios históricos, tais como hospitais e escolas, assim como características geológicas, como rios, lagos e cumes.

Nesta aplicação, o programa **DataIndexer** compila e carrega o índice utilizando uma construção do [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), obtendo o conjunto de dados USGS filtrado a partir de uma Base de Dados SQL pública do Azure. As informações da ligação e das credenciais da fonte de dados online são fornecidas no código do programa. Não é necessária qualquer configuração adicional.

> [!NOTE]
> Aplicamos um filtro neste conjunto de dados para se manter no limite de 10 000 documentos do escalão de preço gratuito. Se utilizar o escalão standard, este limite não é aplicável. Para obter detalhes sobre a capacidade para cada escalão de preço, consulte [limites do Serviço de Pesquisa](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome de serviço e a chave de API do serviço da Azure Search
Depois de criar o serviço, volte ao portal para obter o URL ou `api-key`. As ligações ao serviço Search requerem que tenha o URL e uma `api-key` para autenticar a chamada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra de índice, clique em **Serviço de pesquisa** para listar todos os serviços Azure Search aprovisionados para a sua subscrição.
3. Selecione o serviço que pretende utilizar.
4. No dashboard do serviço, deve os mosaicos com informações essenciais, como o ícone da chave para aceder às chaves de administração.
5. Copie o URL de serviço, uma chave de administração e uma chave de consulta. Os três são necessários mais tarde quando os adicionar ao ficheiro config.js.

## <a name="download-the-sample-files"></a>Transferir os ficheiros de exemplo
Utilize uma das seguintes abordagens para transferir o exemplo.

1. Vá para [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Clique em **Transferir ZIP**, guarde o ficheiro .zip e, em seguida, extraia todos os ficheiros nele contidos.

Todas as modificações do ficheiro e instruções de execução subsequentes são realizadas em ficheiros nesta pasta.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Atualizar o config.js. com o seu URL do serviço Search e a chave de API
Através do URL e da chave de API que copiou anteriormente, especifique o URL, a chave de administração e a chave de consulta no ficheiro de configuração.

As chaves de administração concedem um controlo total sobre as operações de serviço, incluindo criar ou eliminar um índice e carregar documentos. Em contrapartida, as chaves de consulta são para operações só de leitura, normalmente utilizadas por aplicações cliente que se ligam à Azure Search.

Neste exemplo, podemos incluir a chave de consulta para ajudar a reforçar a melhor prática de utilização da chave de consulta em aplicações cliente.

A seguinte captura de ecrã mostra o ficheiro **config.js** aberto num editor de texto, com as entradas relevantes demarcadas para que possa ver onde deve atualizar o ficheiro com os valores válidos para o seu serviço de pesquisa.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Alojar um ambiente de tempo de execução para o exemplo
O exemplo requer um servidor HTTP, que pode instalar globalmente utilizando npm.

Utilize uma janela do PowerShell para os seguintes comandos.

1. Navegue até à pasta que contém o ficheiro **package.json**.
2. Digite `npm install`.
3. Digite `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Compilar o índice e executar a aplicação
1. Digite `npm run indexDocuments`.
2. Digite `npm run build`.
3. Digite `npm run start_server`.
4. Direcione o seu browser para `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Pesquisar nos dados USGS
O conjunto de dados USGS inclui registos relevantes para o Estado da Rhode Island. Se clicar em **Pesquisar** numa caixa de pesquisa em branco, vai obter as 50 entradas principais, o valor predefinido.

Ao introduzir um termo de pesquisa, o motor de busca tem algo concreto para procurar. Tente introduzir um nome regional. "Roger Williams" foi o primeiro governador de Rhode Island. Existem vários parques, edifícios e escolas com o seu nome.

![][9]

Também pode tentar qualquer um destes termos:

* Pawtucket
* Pembroke
* ganso +cabo

## <a name="next-steps"></a>Passos Seguintes
Este é o primeiro tutorial da Azure Search com base em Node.js e o conjunto de dados USGS. Ao longo do tempo, iremos irá expandir este tutorial para demonstrar funcionalidades adicionais de pesquisa que pode querer utilizar nas suas soluções personalizadas.

Se já tiver algum conhecimento sobre a Azure Search, pode utilizar este exemplo como ponto de partida para tentar sugestores (escrita antecipada ou consultas de conclusão automática), filtros e navegação por facetas. Também pode melhorar a página de resultados da pesquisa ao adicionar contagens e criação de batches de documentos para que os utilizadores possam percorrer os resultados.

Novo na Azure Search? Recomendamos que experimente outros tutoriais para desenvolver uma noção básica sobre o que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png

---
title: Introdução ao Azure Search em Java - Azure Search
description: Como compilar uma aplicação de pesquisa na nuvem alojada no Azure utilizando Java como linguagem de programação.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: d16f20e3c2dfa3d670006e44f0072a3871d41c3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289880"
---
# <a name="get-started-with-azure-search-in-java"></a>Introdução à Azure Search em Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Saiba como compilar uma aplicação de pesquisa Java personalizada que utiliza a Azure Search pela sua experiência de pesquisa. Este tutorial utiliza a [API REST do Serviço da Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações utilizados neste exercício.

Para executar este exemplo, precisa de um serviço da Azure Search e, para isso, pode inscrever-se no [Portal do Azure](https://portal.azure.com). Consulte [Criar um serviço da Azure Search no portal](search-create-service-portal.md) para obter instruções passo a passo.

O seguinte software é utilizado para compilar e testar este exemplo:

* [IDE Eclipse para Programadores de Java EE](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Certifique-se de que transfere a versão EE. Um dos passos de verificação requer uma funcionalidade que se encontra apenas nesta edição.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Sobre os dados
Esta aplicação de exemplo utiliza dados dos [Serviços Geológicos dos Estados Unidos (USGS)](https://geonames.usgs.gov/domestic/download_data.htm) filtrados no estado de Rhode Island de forma a reduzir o tamanho do conjunto de dados. Utilizaremos estes dados para compilar uma aplicação de pesquisa que devolve edifícios históricos, tais como hospitais e escolas, assim como características geológicas, como rios, lagos e cumes.

Nesta aplicação, o programa **SearchServlet.java** compila e carrega o índice utilizando uma construção do [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), obtendo o conjunto de dados USGS filtrado a partir de uma Base de Dados SQL pública do Azure. As credenciais predefinidas e as informações da ligação da origem de dados online são fornecidas no código do programa. Em termos de acesso a dados, não é necessária qualquer configuração adicional.

> [!NOTE]
> Aplicamos um filtro neste conjunto de dados para se manter no limite de 10 000 documentos do escalão de preço gratuito. Se utilizar o escalão standard, este limite não é aplicável e pode modificar este código para utilizar um conjunto de dados maior. Para obter detalhes sobre a capacidade para cada escalão de preço, consulte [Limites e restrições](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Sobre os ficheiros de programa
A lista seguinte descreve os ficheiros relevantes para este exemplo.

* Search.jsp: Fornece a interface do usuário
* SearchServlet.java: Fornece métodos (semelhantes a um controlador em MVC)
* SearchServiceClient.java: Processa os pedidos HTTP
* SearchServiceHelper.java: Uma classe auxiliar que fornece métodos estáticos
* Document.java: Fornece o modelo de dados
* config.properties: A pesquisa de conjuntos do serviço URL e a chave de api
* pom.xml: Uma dependência Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome de serviço e a chave de API do serviço da Azure Search
Todas as chamadas da API REST na Azure Search requerem que forneça o URL do serviço e uma chave de API. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra de índice, clique em **Serviço de pesquisa** para listar todos os serviços Azure Search aprovisionados para a sua subscrição.
3. Selecione o serviço que pretende utilizar.
4. No dashboard do serviço, verá os mosaicos com informações essenciais, bem como o ícone da chave para aceder às chaves de administração.
   
      ![][3]
5. Copie o URL de serviço e uma chave de administração. Vai precisar deles mais tarde, quando os adicionar ao ficheiro **config.properties**.

## <a name="download-the-sample-files"></a>Transferir os ficheiros de exemplo
1. Aceda a [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) no GitHub.
2. Clique em **Transferir ZIP**, guarde o ficheiro .zip no disco e, em seguida, extraia todos os ficheiros nele contidos. Considere extrair os ficheiros para a sua área de trabalho Java para tornar mais fácil localizar o projeto mais tarde.
3. Os ficheiros de exemplo são só de leitura. Clique com o botão direito nas propriedades da pasta e desmarque o atributo só de leitura.

Todas as modificações do ficheiro e instruções de execução subsequentes serão realizadas em ficheiros nesta pasta.  

## <a name="import-project"></a>Importar projeto
1. No Eclipse, selecione **Ficheiro** > **Importar** > **Geral** > **Projetos Existentes na Área de Trabalho**.
   
    ![][4]
2. Em **Selecionar diretório de raiz**, navegue para a pasta que contém ficheiros de exemplo. Selecione a pasta que contém a pasta projeto. O projeto deve aparecer na lista **Projetos** como um item selecionado.
   
    ![][12]
3. Clique em **Concluir**.
4. Utilize o **Explorador de Projeto** para ver e editar os ficheiros. Se ainda não estiver aberto, clique em **Janela** > **Mostrar Vista** > **Explorador do Projeto** ou utilize o atalho para abri-lo.

## <a name="configure-the-service-url-and-api-key"></a>Configurar o URL do serviço e a chave de API
1. No **Explorador do Projeto**, faça duplo clique no ficheiro **config.properties** para editar as definições de configuração que contêm o nome do servidor e a chave de API.
2. Consulte os passos descritos anteriormente neste artigo para localizar o URL do serviço e a chave de API no [Portal do Azure](https://portal.azure.com) e obter os valores que irá agora introduzir no ficheiro **config.properties**.
3. Em **config.properties**, substitua "Chave de API" pela chave de API para o serviço. Em seguida, o nome do serviço (o primeiro componente do URL https://servicename.search.windows.net) substitui o "serviço nome" no mesmo ficheiro.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurar os ambientes de tempo de execução, compilação e projeto
1. No Eclipse, no Explorador de Projeto, clique com o botão direito no projeto > **Propriedades** > **Facetas do Projeto**.
2. Selecione **Módulo Dinâmico Web**, **Java** e **JavaScript**.
   
    ![][6]
3. Clique em **Aplicar**.
4. Selecione **Janela** > **Preferências** > **Servidor** > **Ambientes do Tempo de Execução** > **Adicionar..**.
5. Expanda o Apache e selecione a versão do servidor Apache Tomcat que instalou anteriormente. No nosso sistema está instalada a versão 8.
   
    ![][7]
6. Na página seguinte, especifique o diretório de instalação Tomcat. Num computador Windows, é muito provável que seja C:\Programas\Apache Software Foundation\Tomcat *versão*.
7. Clique em **Concluir**.
8. Selecione **Janela** > **Preferências** > **Java** > **JREs Instalados** > **Adicionar**.
9. Em **Adicionar JRE**, selecione **VM Standard**.
10. Clique em **Seguinte**.
11. Em Definição de JRE, na página inicial do JRE, clique em **Diretório**.
12. Navegue até **Programas** > **Java** e selecione o JDK anteriormente instalado. É importante selecionar o JDK assim como o JRE.
13. Em JREs Instalados, selecione o **JDK**. As suas definições devem ter um aspeto semelhante à captura de ecrã seguinte.
    
    ![][9]
14. Opcionalmente, selecione **Janela** > **Browser** > **Internet Explorer** para abrir a aplicação numa janela de browser externo. Utilizar um browser externo dá-lhe uma melhor experiência de aplicação Web.
    
    ![][8]

Agora concluiu as tarefas de configuração. Em seguida, terá de compilar e executar o projeto.

## <a name="build-the-project"></a>Compilar o projeto
1. No Explorador de Projeto, clique com o botão direito no nome do projeto e selecione **Executar Como** > **Compilação Maven…** para configurar o projeto.
   
    ![][10]
2. Em Editar Configuração, em Objetivos, digite "instalação de raiz" e, em seguida, clique em **Executar**.

As mensagens de estado são enviadas para a janela da consola. Deverá ver COMPILAÇÃO COM ÊXITO, o que indica que o projeto foi compilado sem erros.

## <a name="run-the-app"></a>Executar a aplicação
Neste último passo, irá executar a aplicação num ambiente de tempo de execução de servidor local.

Se ainda não especificou um ambiente de tempo de tempo de execução de servidor no Eclipse, terá de fazer isso primeiro.

1. No Explorador de Projeto, expanda **WebContent**.
2. Clique com o botão direito em **Search.jsp** > **Executar Como** > **Executar no Servidor**. Selecione o servidor Apache Tomcat e, em seguida, clique em **Executar**.

> [!TIP]
> Caso tenha utilizado uma área de trabalho não predefinida para armazenar o projeto, terá de modificar **Executar Configuração** para apontar para a localização do projeto para evitar um erro de arranque do servidor. No Explorador de Projeto, clique com o botão direito em **Search.jsp** > **Executar Como** > **Executar Configurações**. Selecione o servidor Apache Tomcat. Clique em **Argumentos**. Clique em **Área de Trabalho** ou **Sistema de Ficheiros** para definir a pasta que contém o projeto.
> 
> 

Quando executa a aplicação, deverá ver uma janela do browser com uma caixa de pesquisa para introduzir termos.

Aguarde cerca de um minuto antes de clicar em **Pesquisar** para dar tempo ao serviço para criar e carregar o índice. Se obtiver um erro HTTP 404, apenas terá de aguardar um pouco mais antes de tentar novamente.

## <a name="search-on-usgs-data"></a>Pesquisar nos dados USGS
O conjunto de dados USGS inclui registos relevantes para o Estado da Rhode Island. Se clicar em **Pesquisar** numa caixa de pesquisa em branco, irá obter as 50 entradas principais, o valor predefinido.

Ao introduzir um termo de pesquisa, o motor de busca terá algo concreto para procurar. Tente introduzir um nome regional. "Roger Williams" foi o primeiro governador de Rhode Island. Existem vários parques, edifícios e escolas com o seu nome.

![][11]

Também pode tentar qualquer um destes termos:

* Pawtucket
* Pembroke
* ganso +cabo

## <a name="next-steps"></a>Passos Seguintes
Este é o primeiro tutorial da Azure Search com base em Java e o conjunto de dados USGS. Ao longo do tempo, iremos irá expandir este tutorial para demonstrar funcionalidades adicionais de pesquisa que pode querer utilizar nas suas soluções personalizadas.

Se já tiver algum conhecimento sobre a Azure Search, pode utilizar este exemplo como ponto de partida para uma experimentação adicional, talvez aumentar a [página de pesquisa](search-pagination-page-layout.md) ou implementar a [navegação por facetas](search-faceted-navigation.md). Também pode melhorar a página de resultados da pesquisa ao adicionar contagens e criação de batches de documentos para que os utilizadores possam percorrer os resultados.

Novo na Azure Search? Recomendamos que experimente outros tutoriais para desenvolver uma noção básica sobre o que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

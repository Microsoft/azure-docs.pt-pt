---
title: Gerir os serviços Web
titleSuffix: ML Studio (classic) - Azure
description: Gerencie os seus serviços Web De Aprendizagem Automática Novos e Clássicos utilizando o portal Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web Clássicos e os novos serviços Web são baseados em diferentes tecnologias subjacentes, você tem capacidades de gestão ligeiramente diferentes para cada um deles.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217963"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Gerencie um serviço web utilizando o portal Azure Machine Learning Studio (clássico) Web Services

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pode gerir os seus serviços Web Machine Learning New and Classic utilizando o portal Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web Clássicos e os novos serviços Web são baseados em diferentes tecnologias subjacentes, você tem capacidades de gestão ligeiramente diferentes para cada um deles.

No portal de Serviços Web de Aprendizagem automática pode:

* Monitorize a forma como o serviço web está a ser utilizado.
* Configure a descrição, atualize as teclas para o serviço web (apenas nova), atualize a chave da sua conta de armazenamento (apenas nova), ative o registo e ative ou desative os dados da amostra.
* Elimine o serviço web.
* Criar, eliminar ou atualizar planos de faturação (apenas novos).
* Adicionar e eliminar pontos finais (apenas clássicos)

>[!NOTE]
>Também pode gerir os serviços web Clássicos no [Machine Learning Studio (clássico)](https://studio.azureml.net) no separador de **serviços Web.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerir serviços web baseados em New Resources Manager

Novos serviços web são implantados como recursos Azure. Como tal, deve ter as permissões corretas para implementar e gerir novos serviços web.  Para implementar ou gerir Novos serviços web deve ser-lhe atribuído um papel de contribuinte ou administrador na subscrição à qual o serviço web é implementado. Se convidar outro utilizador para um espaço de trabalho de aprendizagem automática, deve atribuí-lo a um papel de contribuinte ou administrador na subscrição antes de poder implementar ou gerir serviços web. 

Se o utilizador não tiver as permissões corretas para aceder aos recursos no portal azure Machine Learning Web Services, receberá o seguinte erro ao tentar implementar um serviço web:

*A implementação do Serviço Web falhou. Esta conta não tem acesso suficiente à subscrição do Azure que contém o Workspace. Para implementar um Serviço Web para o Azure, a mesma conta deve ser convidada para o Espaço de Trabalho e ter acesso à subscrição Azure que contém o Espaço de Trabalho.*

Para obter mais informações sobre a criação de um espaço de trabalho, consulte Create e partilhe um espaço de [trabalho azure Machine Learning Studio (clássico).](create-workspace.md)

Para obter mais informações sobre a definição de permissões de acesso, consulte [Gerir o acesso utilizando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Gerir novos serviços Web
Para gerir os seus novos serviços Web:

1. Inscreva-se no portal [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) utilizando a sua conta Microsoft Azure - utilize a conta associada à subscrição do Azure.
2. No menu, clique em **Serviços Web**.

Isto apresenta uma lista de serviços Web implantados para a sua subscrição. 

Para gerir um serviço Web, clique em Web Services. A partir da página de Serviços Web pode:

* Clique no serviço web para geri-lo.
* Clique no Plano de Faturação para o serviço web para atualizá-lo.
* Apague um serviço web.
* Copie um serviço web e desemque-o para outra região.

Quando clica num serviço web, a página quickstart do serviço web abre. A página quickstart do serviço web tem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** - Permite-lhe visualizar o uso do serviço Web.
* **CONFIGURE** - Permite-lhe adicionar texto descritivo, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar ou desativar os dados da amostra.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorização da forma como o serviço web está a ser utilizado
Clique no **separador DASHBOARD.**

A partir do painel de instrumentos, pode visualizar o uso geral do seu serviço Web durante um período de tempo. Pode selecionar o período a visualizar a partir do menu de dropdown period no canto superior direito dos gráficos de utilização. O painel de instrumentos mostra as seguintes informações:

* **Pedidos Ao Longo** do Tempo apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se está a sentir picos de utilização.
* **Pedidos de Resposta a Pedidos** exibe o número total de chamadas de Resposta a Pedido que o serviço recebeu durante o período de tempo selecionado e quantas delas falharam.
* O tempo médio da **computação de resposta ao pedido** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* Os Pedidos de Lote mostram o número total de Pedidos de Lote que o serviço recebeu durante o período de tempo selecionado e **quantos** deles falharam.
* **A Latência Média** de Trabalho apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostram o número agregado de erros que ocorreram nas chamadas para o serviço web.
* **Serviços Custos** exibem os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique na opção de menu **CONFIGURE.**

Pode atualizar as seguintes propriedades:

* **A descrição** permite-lhe introduzir uma descrição para o serviço Web.
* **O título** permite-lhe introduzir um título para o serviço Web
* **As teclas** permitem rodar as suas teclas API primárias e secundárias.
* **A chave da conta** de armazenamento permite-lhe atualizar a chave para a conta de armazenamento associada às alterações do serviço Web. 
* **Ativar os dados da amostra** permite-lhe fornecer dados da amostra que pode utilizar para testar o serviço de Resposta a Pedido. Se criou o serviço web no Machine Learning Studio (clássico), os dados da amostra são retirados dos dados utilizados para treinar o seu modelo. Se criou o serviço programáticamente, os dados são retirados dos dados de exemplo que forneceu como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gestão de planos de faturação
Clique na opção de menu **Planos** a partir da página quickstart dos serviços web. Também pode clicar no plano associado a um serviço Web específico para gerir esse plano.

* **Novo** permite-lhe criar um novo plano.
* **Adicionar/Remover A instância do Plano** permite-lhe "escalar" um plano existente para adicionar capacidade.
* **Upgrade/DownGrade** permite-lhe "escalar" um plano existente para adicionar capacidade.
* **O eliminar** permite-lhe apagar um plano.

Clique num plano para ver o seu painel de instrumentos. O painel de instrumentos dá-lhe uma utilização instantânea ou plana durante um período de tempo selecionado. Para selecionar o período de tempo para visualizar, clique no **período** de paragem no canto superior direito do painel de instrumentos. 

O painel de instrumentos do plano fornece as seguintes informações:

* **Descrição do plano** mostra informações sobre os custos e capacidade associados ao plano.
* **O Plan Usage** apresenta o número de transações e horas de computação que foram cobradas contra o plano.
* **Os Serviços Web** exibem o número de serviços Web que estão a utilizar este plano.
* **O Top Web Service By Calls** exibe os quatro principais serviços Web que estão a fazer chamadas que são cobradas contra o plano.
* **Os principais serviços web da Compute Hrs** exibem os quatro principais serviços Web que estão a usar recursos computacionais que são cobrados contra o plano.

## <a name="manage-classic-web-services"></a>Gerir serviços web clássicos
> [!NOTE]
> Os procedimentos nesta secção são relevantes para a gestão de serviços web clássicos através do portal Azure Machine Learning Web Services. Para obter informações sobre a gestão dos serviços Web Clássicos através do Machine Learning Studio (clássico) e do portal Azure, consulte [Manage a Azure Machine Learning Studio (clássico) espaço](manage-workspace.md)de trabalho.
> 
> 

Para gerir os seus serviços Web Clássicos:

1. Inscreva-se no portal [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) utilizando a sua conta Microsoft Azure - utilize a conta associada à subscrição do Azure.
2. No menu, clique em **Serviços Web Clássicos**.

Para gerir um Serviço Web Clássico, clique em **Serviços Web Clássicos**. A partir da página de Serviços Web Clássicos pode:

* Clique no serviço web para ver os pontos finais associados.
* Apague um serviço web.

Quando gere um serviço Web Clássico, gere cada um dos pontos finais separadamente. Quando clica num serviço web na página dos Serviços Web, abre a lista de pontos finais associados ao serviço. 

Na página final do Classic Web Service, pode adicionar e eliminar pontos finais no serviço. Para obter mais informações sobre a adição de pontos [finais, consulte Criar Pontos Finais](create-endpoint.md).

Clique num dos pontos finais para abrir a página quickstart do serviço web. Na página Quickstart, existem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** - Permite-lhe visualizar o uso do serviço Web.
* **CONFIGURE** - Permite-lhe adicionar texto descritivo, ligar e desligar o registo de erros, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar e desativar os dados da amostra.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorização da forma como o serviço web está a ser utilizado
Clique no **separador DASHBOARD.**

A partir do painel de instrumentos, pode visualizar o uso geral do seu serviço Web durante um período de tempo. Pode selecionar o período a visualizar a partir do menu de dropdown period no canto superior direito dos gráficos de utilização. O painel de instrumentos mostra as seguintes informações:

* **Pedidos Ao Longo** do Tempo apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se está a sentir picos de utilização.
* **Pedidos de Resposta a Pedidos** exibe o número total de chamadas de Resposta a Pedido que o serviço recebeu durante o período de tempo selecionado e quantas delas falharam.
* O tempo médio da **computação de resposta ao pedido** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* Os Pedidos de Lote mostram o número total de Pedidos de Lote que o serviço recebeu durante o período de tempo selecionado e **quantos** deles falharam.
* **A Latência Média** de Trabalho apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostram o número agregado de erros que ocorreram nas chamadas para o serviço web.
* **Serviços Custos** exibem os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique na opção de menu **CONFIGURE.**

Pode atualizar as seguintes propriedades:

* **A descrição** permite-lhe introduzir uma descrição para o serviço Web. A descrição é um campo obrigatório.
* **O registo** permite-lhe ativar ou desativar o registo de erros no ponto final. Para obter mais informações sobre a exploração madeireira, consulte A [exploração de login para serviços web de aprendizagem automática](web-services-logging.md).
* **Ativar os dados da amostra** permite-lhe fornecer dados da amostra que pode utilizar para testar o serviço de Resposta a Pedido. Se criou o serviço web no Machine Learning Studio (clássico), os dados da amostra são retirados dos dados utilizados para treinar o seu modelo. Se criou o serviço programáticamente, os dados são retirados dos dados de exemplo que forneceu como parte do pacote JSON.



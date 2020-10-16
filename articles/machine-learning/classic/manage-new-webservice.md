---
title: 'ML Studio (clássico): Gerir serviços web - Azure'
description: Gerencie os seus serviços Web machine learning novos e clássicos utilizando o portal Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web Clássicos e os serviços New Web são baseados em diferentes tecnologias subjacentes, você tem capacidades de gestão ligeiramente diferentes para cada um deles.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: caaf8e25cdf43602fda8fc72caf7d16b7b56c06a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361896"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Gerir um serviço web utilizando o portal Azure Machine Learning Studio (clássico) Web Services

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../compare-azure-ml-to-studio-classic.md)  


Pode gerir os seus serviços Web Machine Learning New e Classic usando o portal Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web Clássicos e os serviços New Web são baseados em diferentes tecnologias subjacentes, você tem capacidades de gestão ligeiramente diferentes para cada um deles.

No portal machine learning Web Services pode:

* Monitorize a forma como o serviço web está a ser utilizado.
* Configure a descrição, atualize as chaves do serviço web (apenas novo), atualize a chave da sua conta de armazenamento (apenas nova), permita o registo e ative ou desative os dados da amostra.
* Apague o serviço web.
* Criar, eliminar ou atualizar planos de faturação (apenas novos).
* Adicionar e apagar pontos finais (apenas clássico)

>[!NOTE]
>Também pode gerir serviços web clássicos no [Machine Learning Studio (clássico)](https://studio.azureml.net) no separador **serviços Web.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerir novos serviços web baseados em Novos Recursos

Novos serviços web são implantados como recursos Azure. Como tal, deve ter as permissões corretas para implementar e gerir novos serviços web.  Para implementar ou gerir Novos serviços web, deve ser-lhe atribuída uma função de contribuinte ou administrador na subscrição para a qual o serviço web é implantado. Se convidar outro utilizador para um espaço de trabalho de machine learning, deve atribuí-lo a um colaborador ou administrador na subscrição antes de poder implementar ou gerir serviços web. 

Se o utilizador não tiver as permissões corretas para aceder aos recursos no portal Azure Machine Learning Web Services, receberá o seguinte erro ao tentar implementar um serviço web:

*A implementação do Serviço Web falhou. Esta conta não tem acesso suficiente à subscrição do Azure que contém o Espaço de Trabalho. Para implementar um Serviço Web para o Azure, a mesma conta deve ser convidada para o Espaço de Trabalho e ter acesso à subscrição do Azure que contém o Espaço de Trabalho.*

Para obter mais informações sobre a criação de um espaço de trabalho, consulte Criar e partilhar um espaço de [trabalho (clássico) do Azure Machine Learning Studio](create-workspace.md)( clássico).

Para obter mais informações sobre a definição de permissões de acesso, consulte [Gerir o acesso através do RBAC e do portal Azure.](../../role-based-access-control/role-assignments-portal.md)


## <a name="manage-new-web-services"></a>Gerir novos serviços Web
Para gerir os seus novos serviços Web:

1. Inscreva-se no portal [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) utilizando a sua conta Microsoft Azure - utilize a conta que está associada à subscrição do Azure.
2. No menu, clique em **Serviços Web.**

Isto apresenta uma lista de serviços Web implantados para a sua subscrição. 

Para gerir um serviço Web, clique em Serviços Web. A partir da página de Serviços Web pode:

* Clique no serviço web para geri-lo.
* Clique no Plano de Faturação para o serviço web para atualizá-lo.
* Apague um serviço web.
* Copie um serviço web e desloque-o para outra região.

Quando clica num serviço web, a página quickstart do serviço web abre. A página quickstart do serviço web tem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** - Permite-lhe visualizar o uso do serviço Web.
* **CONFIGURE** - Permite-lhe adicionar texto descritivo, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar ou desativar os dados da amostra.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique no **separador DASHBOARD.**

A partir do painel de instrumentos, pode visualizar o uso geral do seu serviço Web durante um período de tempo. Pode selecionar o período para visualizar a partir do menu de entrega do período no canto superior direito das tabelas de utilização. O painel de instrumentos mostra as seguintes informações:

* **Pedidos Ao longo do tempo** apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se está a experimentar picos de uso.
* **Pedidos de Resposta** a Pedidos exibem o número total de chamadas Request-Response que o serviço recebeu durante o período de tempo selecionado e quantas delas falharam.
* **O tempo de Request-Response computacional** médio apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Os Pedidos** de Lote exibem o número total de Pedidos de Lote que o serviço recebeu ao longo do período de tempo selecionado e quantos deles falharam.
* **A Média de Latência** do Trabalho apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Os erros** mostram o número agregado de erros ocorridos nas chamadas para o serviço web.
* **Serviços Os custos** exibem os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique na opção menu **CONFIGURE.**

Pode atualizar as seguintes propriedades:

* **A descrição** permite-lhe introduzir uma descrição para o serviço Web.
* **O título** permite-lhe introduzir um título para o serviço Web
* **As teclas** permitem-lhe rodar as suas teclas API primárias e secundárias.
* **A chave da conta de armazenamento** permite-lhe atualizar a chave para a conta de armazenamento associada às alterações do serviço Web. 
* **Ativar os dados da amostra** permite-lhe fornecer dados de amostra que pode utilizar para testar o serviço Request-Response. Se criou o serviço web no Machine Learning Studio (clássico), os dados da amostra são retirados dos dados utilizados para treinar o seu modelo. Se criou o serviço programáticamente, os dados são retirados dos dados de exemplo fornecidos como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gestão de planos de faturação
Clique na opção de menu **Planos** a partir da página quickstart dos serviços web. Também pode clicar no plano associado a um serviço Web específico para gerir esse plano.

* **O** novo permite-lhe criar um novo plano.
* **A instância do Plano de Adicionar/Remover** permite-lhe "escalar" um plano existente para aumentar a capacidade.
* **O Upgrade/DownGrade** permite-lhe "escalar" um plano existente para aumentar a capacidade.
* **A eliminação** permite-lhe eliminar um plano.

Clique num plano para ver o seu painel de instrumentos. O painel de instrumentos dá-lhe instantâneo ou planeia a utilização durante um período de tempo selecionado. Para selecionar o período de tempo para visualizar, clique no **período** de entrega no canto superior direito do painel. 

O painel de instrumentos do plano fornece as seguintes informações:

* **A Descrição do Plano** apresenta informações sobre os custos e capacidades associadas ao plano.
* **O Uso do Plano** apresenta o número de transações e horas de cálculo que foram cobradas contra o plano.
* **Os Serviços Web** exibem o número de serviços Web que estão a utilizar este plano.
* **Top Web Service By Calls** exibe os quatro principais serviços Web que estão a fazer chamadas que são cobradas contra o plano.
* **Top Web Services by Compute Hrs** exibe os quatro principais serviços Web que estão usando recursos compute que são cobrados contra o plano.

## <a name="manage-classic-web-services"></a>Gerir serviços web clássicos
> [!NOTE]
> Os procedimentos nesta secção são relevantes para a gestão dos serviços web clássicos através do portal Azure Machine Learning Web Services. Para obter informações sobre a gestão dos serviços Web Clássicos através do Machine Learning Studio (clássico) e do portal Azure, consulte [Gerir um espaço de trabalho do Azure Machine Learning Studio (clássico).](manage-workspace.md)
> 
> 

Para gerir os seus serviços Web Clássicos:

1. Inscreva-se no portal [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) utilizando a sua conta Microsoft Azure - utilize a conta que está associada à subscrição do Azure.
2. No menu, clique em **Classic Web Services**.

Para gerir um Serviço Web Clássico, clique em **Classic Web Services**. A partir da página De Serviços Web Clássicos pode:

* Clique no serviço web para ver os pontos finais associados.
* Apague um serviço web.

Quando gere um serviço Web Clássico, gere cada um dos pontos finais separadamente. Quando clica num serviço web na página de Serviços Web, abre-se a lista de pontos finais associados ao serviço. 

Na página de ponta do Serviço Web Clássico, pode adicionar e eliminar pontos finais no serviço. Para obter mais informações sobre a adição de pontos finais, consulte [Creating Endpoints](create-endpoint.md).

Clique num dos pontos finais para abrir a página Quickstart do serviço web. Na página Quickstart, existem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** - Permite-lhe visualizar o uso do serviço Web.
* **CONFIGURE** - Permite-lhe adicionar texto descritivo, ligar e desligar o erro, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar e desativar os dados da amostra.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique no **separador DASHBOARD.**

A partir do painel de instrumentos, pode visualizar o uso geral do seu serviço Web durante um período de tempo. Pode selecionar o período para visualizar a partir do menu de entrega do período no canto superior direito das tabelas de utilização. O painel de instrumentos mostra as seguintes informações:

* **Pedidos Ao longo do tempo** apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se está a experimentar picos de uso.
* **Pedidos de Resposta** a Pedidos exibem o número total de chamadas Request-Response que o serviço recebeu durante o período de tempo selecionado e quantas delas falharam.
* **O tempo de Request-Response computacional** médio apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Os Pedidos** de Lote exibem o número total de Pedidos de Lote que o serviço recebeu ao longo do período de tempo selecionado e quantos deles falharam.
* **A Média de Latência** do Trabalho apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Os erros** mostram o número agregado de erros ocorridos nas chamadas para o serviço web.
* **Serviços Os custos** exibem os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique na opção menu **CONFIGURE.**

Pode atualizar as seguintes propriedades:

* **A descrição** permite-lhe introduzir uma descrição para o serviço Web. A descrição é um campo obrigatório.
* **O registo** de registo permite-lhe ativar ou desativar a sessão de registo de erros no ponto final. Para obter mais informações sobre o Registo registador, consulte [Ativar o registo de serviços web de Machine Learning](web-services-logging.md).
* **Ativar os dados da amostra** permite-lhe fornecer dados de amostra que pode utilizar para testar o serviço Request-Response. Se criou o serviço web no Machine Learning Studio (clássico), os dados da amostra são retirados dos dados utilizados para treinar o seu modelo. Se criou o serviço programáticamente, os dados são retirados dos dados de exemplo fornecidos como parte do pacote JSON.



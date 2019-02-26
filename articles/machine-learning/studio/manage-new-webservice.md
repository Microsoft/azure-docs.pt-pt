---
title: Utilizar o portal de serviços Web do Azure Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Pode gerenciar seu novo do Machine Learning e serviços Web clássicos com o portal do Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web clássicos e serviços Web novos baseiam-se nos diferentes tecnologias subjacentes, tem funcionalidades de gestão ligeiramente diferente para cada uma delas.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2d62b5a140fc4614e26d40f153f5e13d4a3b70f4
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817155"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-web-services-portal"></a>Gerir um serviço web com o portal de serviços Web do Azure Machine Learning Studio
Pode gerenciar seu novo do Machine Learning e serviços Web clássicos com o portal do Microsoft Azure Machine Learning Web Services. Uma vez que os serviços Web clássicos e serviços Web novos baseiam-se nos diferentes tecnologias subjacentes, tem funcionalidades de gestão ligeiramente diferente para cada uma delas.

No portal do Machine Learning Web Services, pode:

* Monitorize a forma como o serviço web está a ser utilizado.
* Configurar a descrição, Atualize as chaves para a web service (apenas para o novo), atualize seu armazenamento conta chave (New apenas), ativar o registo e ativar ou desativar a dados de exemplo.
* Elimine o serviço web.
* Criar, delete ou update faturação planos (apenas para o novo).
* Adicionar e eliminar os pontos de extremidade (apenas clássica)

>[!NOTE]
>Também pode gerir serviços web clássico [Machine Learning Studio](http://studio.azureml.net) sobre o **serviços Web** separador.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerir o novo Gestor de recursos com base em serviços da web

Novos serviços web são implementados como recursos do Azure. Como tal, tem de ter as permissões corretas para implementar e gerir novos serviços web.  Para implementar ou gerir os novos serviços web tem de ser atribuída uma função de Contribuidor ou administrador da subscrição em que o serviço web é implementado. Se convidar outro utilizador para uma área de trabalho de aprendizagem automática, deve atribuí-las a uma função de Contribuidor ou administrador da subscrição para poder implementar ou gerir os serviços web. 

Se o utilizador não tem as permissões corretas para aceder a recursos no portal do Azure Machine Learning Web Services, eles irão receber o seguinte erro ao tentar implementar um serviço web:

*Falha na implementação do serviço Web. Esta conta não tem acesso suficiente para a subscrição do Azure que contém a área de trabalho. Para poder implementar um serviço Web para o Azure, a mesma conta têm de ser convidada para a área de trabalho e obter acesso à subscrição do Azure que contém a área de trabalho.*

Para obter mais informações sobre como criar uma área de trabalho, consulte [criar e partilhar uma área de trabalho do Azure Machine Learning Studio](create-workspace.md).

Para obter mais informações sobre a definição de permissões de acesso, consulte [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Gerir os serviços Web novo
Para gerir os seus serviços Web novos:

1. Entrar para o [serviços de Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal com o Microsoft Azure da conta – utilizar a conta que está associada à subscrição do Azure.
2. No menu, clique em **serviços da Web**.

Esta ação apresenta uma lista de serviços Web implementados para a sua subscrição. 

Para gerir um serviço da Web, clique em serviços da Web. A página de Web Services, pode:

* Clique no serviço de web para geri-la.
* Clique a faturação planear para o serviço web para atualizá-lo.
* Elimine um serviço web.
* Copie um serviço da web e implementá-la para outra região.

Quando clica num serviço da web, é aberta a página de início rápido de serviço da web. A página de início rápido do serviço web tem duas opções de menu que permitem-lhe gerir o seu serviço web:

* **DASHBOARD** -permite-lhe ver a utilização do serviço Web.
* **CONFIGURAR** – permite-lhe adicionar texto descritivo, atualizar a chave para a conta de armazenamento associada ao serviço Web e ative ou desative os dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique nas **DASHBOARD** separador.

No dashboard, pode ver a utilização global do seu serviço Web durante um período de tempo. Pode selecionar o período para exibir no menu suspenso período no canto superior direito dos gráficos de utilização. O dashboard apresenta as seguintes informações:

* **Pedidos ao longo do tempo** mostra um gráfico de passo do número de pedidos ao longo do período de tempo selecionado. Pode ajudar a identificar se estiver tendo picos de utilização.
* **Pedidos de solicitação-resposta** apresenta o número total de chamadas de solicitação-resposta, o serviço recebeu ao longo do período de tempo selecionado e quantos deles falhou.
* **Tempo médio de pedido-resposta computação** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Pedidos de lote** apresenta o número total de pedidos de lote, o serviço recebeu ao longo do período de tempo selecionado e quantos deles falhou.
* **Média de latência de tarefa** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostra o número agregado de erros que ocorreram em chamadas para o serviço web.
* **Os custos de serviços** apresenta as cobranças do plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique nas **configurar** opção de menu.

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web.
* **Título** permite-lhe introduzir um título para o serviço Web
* **Chaves** permite-lhe rodar as chaves de API primárias e secundárias.
* **Chave da conta de armazenamento** permite-lhe atualizar a chave para a conta de armazenamento associada com as alterações de serviço da Web. 
* **Ativar dados de exemplo** permite-lhe fornecer dados de exemplo que pode utilizar para testar o serviço de solicitação-resposta. Se tiver criado o serviço web no Machine Learning Studio, os dados de exemplo foi obtidos dos dados de sua utilizados para preparar o seu modelo. Se tiver criado o serviço por meio de programação, os dados são retirados dos dados de exemplo fornecido como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gerir planos de faturação
Clique nas **planos** opção de menu da página de início rápido de serviços da web. Também pode clicar o plano associado ao serviço Web específico para gerir esse plano.

* **Novo** permite-lhe criar um novo plano.
* **Instância de plano de adicionar/remover** permite-lhe "aumentar horizontalmente" um plano existente para adicionar capacidade.
* **Atualização/mudança para versão anterior** permite-lhe para "aumentar verticalmente" um plano existente para adicionar capacidade.
* **Eliminar** permite-lhe eliminar um plano.

Clique num plano para ver o seu dashboard. O dashboard dá-lhe instantâneo ou um plano de utilização ao longo de um período de tempo selecionado. Para selecionar o período de tempo para ver, clique a **período** suspensa no canto superior direito do dashboard. 

O dashboard de plano fornece as seguintes informações:

* **Planear a descrição** apresenta informações sobre os custos e a capacidade associados com o plano.
* **Planear a utilização** mostra o número de transações e as horas de computação que tem sido cobradas contra o plano.
* **Serviços Web** mostra o número de serviços da Web que estão a utilizar este plano.
* **Principais por chamadas de serviço Web** apresenta os serviços da Web de melhores que estão fazendo chamadas são cobradas conforme o plano.
* **Principais de serviços da Web por horas de computação** apresenta os serviços de Web de melhores que estiver a utilizar recursos de computação que são cobrados conforme o plano.

## <a name="manage-classic-web-services"></a>Gerir serviços Web clássicos
> [!NOTE]
> Os procedimentos nesta secção são relevantes para a gestão de serviços da web clássico através do portal do Azure Machine Learning Web Services. Para obter informações sobre o gerenciamento de serviços Web clássicos através de Machine Learning Studio e o portal do Azure, consulte [gerir uma área de trabalho do Azure Machine Learning Studio](manage-workspace.md).
> 
> 

Para gerir os seus serviços Web clássicos:

1. Entrar para o [serviços de Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal com o Microsoft Azure da conta – utilizar a conta que está associada à subscrição do Azure.
2. No menu, clique em **serviços Web clássicos**.

Para gerir um serviço Web clássico, clique em **serviços Web clássicos**. A página de serviços Web clássicos, pode:

* Clique no serviço de web para ver os pontos finais associados.
* Elimine um serviço web.

Quando gerir um serviço Web clássico, pode gerir cada um dos pontos finais em separado. Quando clica num serviço da web na página de Web Services, é aberta a lista de pontos finais associados ao serviço. 

Na página de ponto final de serviço Web clássico, pode adicionar e eliminar pontos finais do serviço. Para obter mais informações sobre como adicionar pontos finais, consulte [criando pontos de extremidade](create-endpoint.md).

Clique em um dos pontos finais para abrir a página de início rápido do serviço web. Na página de início rápido, existem duas opções de menu que permitem-lhe gerir o seu serviço web:

* **DASHBOARD** -permite-lhe ver a utilização do serviço Web.
* **CONFIGURAR** - permite-lhe adicionar texto descritivo, ativar o registo de erros e desativar, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar e desativar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique nas **DASHBOARD** separador.

No dashboard, pode ver a utilização global do seu serviço Web durante um período de tempo. Pode selecionar o período para exibir no menu suspenso período no canto superior direito dos gráficos de utilização. O dashboard apresenta as seguintes informações:

* **Pedidos ao longo do tempo** mostra um gráfico de passo do número de pedidos ao longo do período de tempo selecionado. Pode ajudar a identificar se estiver tendo picos de utilização.
* **Pedidos de solicitação-resposta** apresenta o número total de chamadas de solicitação-resposta, o serviço recebeu ao longo do período de tempo selecionado e quantos deles falhou.
* **Tempo médio de pedido-resposta computação** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Pedidos de lote** apresenta o número total de pedidos de lote, o serviço recebeu ao longo do período de tempo selecionado e quantos deles falhou.
* **Média de latência de tarefa** apresenta uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostra o número agregado de erros que ocorreram em chamadas para o serviço web.
* **Os custos de serviços** apresenta as cobranças do plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique nas **configurar** opção de menu.

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web. A descrição é um campo obrigatório.
* **Registo** permite-lhe ativar ou desativar o registo no ponto final de erro. Para obter mais informações sobre o registo, consulte ativar [registar serviços web de Machine Learning](web-services-logging.md).
* **Ativar dados de exemplo** permite-lhe fornecer dados de exemplo que pode utilizar para testar o serviço de solicitação-resposta. Se tiver criado o serviço web no Machine Learning Studio, os dados de exemplo foi obtidos dos dados de sua utilizados para preparar o seu modelo. Se tiver criado o serviço por meio de programação, os dados são retirados dos dados de exemplo fornecido como parte do pacote JSON.



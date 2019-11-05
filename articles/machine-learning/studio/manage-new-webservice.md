---
title: Gerir os serviços Web
titleSuffix: Azure Machine Learning Studio (classic)
description: Gerencie seus Machine Learning serviços Web novos e clássicos usando o portal de serviços Web do Microsoft Azure Machine Learning. Como os serviços Web clássicos e os novos serviços Web são baseados em tecnologias subjacentes diferentes, você tem recursos de gerenciamento um pouco diferentes para cada um deles.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: f0d31ce46ce185ad5bbcd736d9f45e62d7436d24
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489241"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Gerenciar um serviço Web usando o portal de serviços Web Azure Machine Learning Studio (clássico)
Você pode gerenciar seus Machine Learning serviços Web novos e clássicos usando o portal de serviços Web do Microsoft Azure Machine Learning. Como os serviços Web clássicos e os novos serviços Web são baseados em tecnologias subjacentes diferentes, você tem recursos de gerenciamento um pouco diferentes para cada um deles.

No portal de serviços Web do Machine Learning, você pode:

* Monitore como o serviço Web está sendo usado.
* Configure a descrição, atualize as chaves do serviço Web (somente nova), atualize sua chave de conta de armazenamento (somente nova), habilite o registro em log e habilite ou desabilite os dados de exemplo.
* Exclua o serviço Web.
* Criar, excluir ou atualizar planos de cobrança (somente novo).
* Adicionar e excluir pontos de extremidade (somente clássico)

>[!NOTE]
>Você também pode gerenciar serviços Web clássicos no [Machine Learning Studio (clássico)](https://studio.azureml.net) na guia **Serviços Web** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerenciar novos serviços Web baseados no Gerenciador de recursos

Novos serviços Web são implantados como recursos do Azure. Dessa forma, você deve ter as permissões corretas para implantar e gerenciar novos serviços Web.  Para implantar ou gerenciar novos serviços Web, você deve receber uma função de colaborador ou administrador na assinatura na qual o serviço Web está implantado. Se você convidar outro usuário para um espaço de trabalho do Machine Learning, deverá atribuí-los a uma função de colaborador ou de administrador na assinatura para poder implantar ou gerenciar serviços Web. 

Se o usuário não tiver as permissões corretas para acessar recursos no portal de serviços Web Azure Machine Learning, ele receberá o seguinte erro ao tentar implantar um serviço Web:

*Falha na implantação do serviço Web. Essa conta não tem acesso suficiente à assinatura do Azure que contém o espaço de trabalho. Para implantar um serviço Web no Azure, a mesma conta deve ser convidada para o espaço de trabalho e receber acesso à assinatura do Azure que contém o espaço de trabalho.*

Para obter mais informações sobre como criar um espaço de trabalho, consulte [criar e compartilhar um Azure Machine Learning Studio (clássico) espaço de trabalho](create-workspace.md).

Para obter mais informações sobre como definir permissões de acesso, consulte [gerenciar o acesso usando RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Gerenciar novos serviços Web
Para gerenciar seus novos serviços Web:

1. Entre no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure-use a conta que está associada à assinatura do Azure.
2. No menu, clique em **Serviços Web**.

Isso exibe uma lista de serviços Web implantados para sua assinatura. 

Para gerenciar um serviço Web, clique em serviços Web. Na página serviços Web, você pode:

* Clique no serviço Web para gerenciá-lo.
* Clique no plano de cobrança para o serviço Web para atualizá-lo.
* Excluir um serviço Web.
* Copie um serviço Web e implante-o em outra região.

Quando você clica em um serviço Web, a página início rápido do serviço Web é aberta. A página de início rápido do serviço Web tem duas opções de menu que permitem que você gerencie seu serviço Web:

* **Painel** – permite exibir o uso do serviço Web.
* **Configurar** – permite que você adicione texto descritivo, atualize a chave da conta de armazenamento associada ao serviço Web e habilite ou desabilite dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorando como o serviço Web está sendo usado
Clique na guia **painel** .

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período a ser exibido no menu suspenso período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

* **Solicitações ao longo do tempo** exibe um gráfico de etapas do número de solicitações no período de tempo selecionado. Ele pode ajudar a identificar se você está enfrentando picos de uso.
* **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu durante o período de tempo selecionado e quantos deles falharam.
* **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Solicitações em lote** exibe o número total de solicitações de lote que o serviço recebeu durante o período de tempo selecionado e quantos deles falharam.
* **Latência média do trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Erros** exibe o número agregado de erros que ocorreram em chamadas para o serviço Web.
* **Os custos de serviços** exibe os encargos do plano de cobrança associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço Web
Clique na opção de menu **Configurar** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite que você insira uma descrição para o serviço Web.
* O **título** permite que você insira um título para o serviço Web
* **As chaves** permitem que você gire suas chaves de API primárias e secundárias.
* A **chave da conta de armazenamento** permite que você atualize a chave da conta de armazenamento associada às alterações do serviço Web. 
* **Habilitar dados de exemplo** permite que você forneça dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Se você criou o serviço Web no Machine Learning Studio (clássico), os dados de exemplo serão obtidos dos dados que você usou para treinar seu modelo. Se você criou o serviço programaticamente, os dados são extraídos dos dados de exemplo fornecidos como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gerenciando planos de cobrança
Clique na opção de menu **planos** na página início rápido dos serviços Web. Você também pode clicar no plano associado a um serviço Web específico para gerenciar esse plano.

* **Novo** permite que você crie um novo plano.
* **Adicionar/remover instância de plano** permite que você "Escale horizontalmente" um plano existente para adicionar capacidade.
* O **upgrade/DownGrade** permite que você "Escale verticalmente" um plano existente para adicionar capacidade.
* **Excluir** permite que você exclua um plano.

Clique em um plano para exibir seu painel. O painel fornece instantâneo ou uso do plano durante um período de tempo selecionado. Para selecionar o período de tempo a ser exibido, clique na lista suspensa **período** no canto superior direito do painel. 

O painel de plano fornece as seguintes informações:

* **Descrição do plano** exibe informações sobre os custos e a capacidade associados ao plano.
* O **uso do plano** exibe o número de transações e horas de computação que foram cobradas no plano.
* **Serviços Web** exibe o número de serviços Web que estão usando este plano.
* O **principal serviço Web por chamadas** exibe os quatro principais serviços Web que estão fazendo chamadas que são cobradas em relação ao plano.
* **Principais serviços Web por h de computação** exibe os quatro principais serviços Web que estão usando recursos de computação que são cobrados em relação ao plano.

## <a name="manage-classic-web-services"></a>Gerenciar serviços Web clássicos
> [!NOTE]
> Os procedimentos nesta seção são relevantes para o gerenciamento de serviços Web clássicos por meio do portal de serviços Web Azure Machine Learning. Para obter informações sobre como gerenciar serviços Web clássicos por meio do Machine Learning Studio (clássico) e do portal do Azure, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).
> 
> 

Para gerenciar seus serviços Web clássicos:

1. Entre no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure-use a conta que está associada à assinatura do Azure.
2. No menu, clique em **Serviços Web clássicos**.

Para gerenciar um serviço Web clássico, clique em **Serviços Web clássicos**. Na página de serviços Web clássicos, você pode:

* Clique no serviço Web para exibir os pontos de extremidade associados.
* Excluir um serviço Web.

Ao gerenciar um serviço Web clássico, você gerencia cada um dos pontos de extremidade separadamente. Quando você clica em um serviço Web na página serviços Web, a lista de pontos de extremidade associados ao serviço é aberta. 

Na página ponto de extremidade do serviço da Web clássico, você pode adicionar e excluir pontos de extremidade no serviço. Para obter mais informações sobre como adicionar pontos de extremidade, consulte [criando pontos de extremidade](create-endpoint.md).

Clique em um dos pontos de extremidade para abrir a página início rápido do serviço Web. Na página de início rápido, há duas opções de menu que permitem que você gerencie seu serviço Web:

* **Painel** – permite exibir o uso do serviço Web.
* **Configurar** – permite adicionar texto descritivo, ativar e desativar o log de erros, atualizar a chave da conta de armazenamento associada ao serviço Web e habilitar e desabilitar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorando como o serviço Web está sendo usado
Clique na guia **painel** .

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período a ser exibido no menu suspenso período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

* **Solicitações ao longo do tempo** exibe um gráfico de etapas do número de solicitações no período de tempo selecionado. Ele pode ajudar a identificar se você está enfrentando picos de uso.
* **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu durante o período de tempo selecionado e quantos deles falharam.
* **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Solicitações em lote** exibe o número total de solicitações de lote que o serviço recebeu durante o período de tempo selecionado e quantos deles falharam.
* **Latência média do trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Erros** exibe o número agregado de erros que ocorreram em chamadas para o serviço Web.
* **Os custos de serviços** exibe os encargos do plano de cobrança associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço Web
Clique na opção de menu **Configurar** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite que você insira uma descrição para o serviço Web. Descrição é um campo obrigatório.
* O **registro em log** permite habilitar ou desabilitar o log de erros no ponto de extremidade. Para obter mais informações sobre registro em log, consulte Habilitar o [log para serviços web Machine Learning](web-services-logging.md).
* **Habilitar dados de exemplo** permite que você forneça dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Se você criou o serviço Web no Machine Learning Studio (clássico), os dados de exemplo serão obtidos dos dados que você usou para treinar seu modelo. Se você criou o serviço programaticamente, os dados são extraídos dos dados de exemplo fornecidos como parte do pacote JSON.



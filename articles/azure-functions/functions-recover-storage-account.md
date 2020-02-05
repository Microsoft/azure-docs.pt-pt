---
title: Como solucionar problemas Azure Functions Runtime está inacessível.
description: Saiba como solucionar problemas de uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963891"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Como solucionar problemas "o tempo de execução do Functions está inacessível"

Este artigo destina-se a resolver problemas com a mensagem de erro "functions time is inreachable" quando é exibida no portal Azure. Quando este erro ocorrer, vê a seguinte cadeia de erro exibida no portal.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Isto ocorre quando o tempo de funcionamento das funções Azure não pode começar. O motivo mais comum para esse erro ocorrer é o aplicativo de função que está perdendo o acesso à sua conta de armazenamento. Para saber mais, consulte os requisitos da [conta de armazenamento.](storage-considerations.md#storage-account-requirements)

O resto deste artigo ajuda-o a resolver as seguintes causas deste erro, incluindo como identificar e resolver cada caso.

+ [Storage account deleted](#storage-account-deleted) (Conta de armazenamento eliminada)
+ [Storage account application settings deleted](#storage-account-application-settings-deleted) (Definições da aplicação da conta de armazenamento eliminadas)
+ [Storage account credentials invalid](#storage-account-credentials-invalid) (Credenciais da conta de armazenamento inválidas)
+ [Storage account inaccessible](#storage-account-inaccessible) (Conta de armazenamento inacessível)
+ [Quota de execução diária excedida](#daily-execution-quota-full)
+ [A sua aplicação está atrás de uma firewall](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Conta de armazenamento excluída

Cada aplicativo de funções requer uma conta de armazenamento para operar. Se essa conta for excluída, sua função não funcionará.

### <a name="how-to-find-your-storage-account"></a>Como localizar sua conta de armazenamento

Comece pesquisando o nome da conta de armazenamento nas configurações do aplicativo. Ou `AzureWebJobsStorage` ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterão o nome da sua conta de armazenamento embrulhado numa cadeia de ligação. Leia mais detalhes na referência da [definição de aplicação aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Pesquise sua conta de armazenamento na portal do Azure para ver se ela ainda existe. Se ele tiver sido excluído, será necessário recriar uma conta de armazenamento e substituir as cadeias de conexão de armazenamento. Seu código de função é perdido e você precisará reimplantá-lo novamente.

## <a name="storage-account-application-settings-deleted"></a>Configurações do aplicativo da conta de armazenamento excluídas

Na etapa anterior, se você não tiver uma cadeia de conexão da conta de armazenamento, ela provavelmente foi excluída ou substituída. A exclusão das configurações do aplicativo é mais comumente feita ao usar slots de implantação ou Azure Resource Manager scripts para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo necessárias

* Necessário
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para funções de plano de consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Leia aqui estas definições de aplicação](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Orientação

* Não verifique "configuração de slot" para qualquer destas definições. Quando troca as ranhuras de implementação, a aplicação de função quebra.
* Não modifique estas definições como parte de implementações automatizadas.
* Essas configurações devem ser fornecidas e válidas no momento da criação. Uma implementação automatizada que não contenha estas definições resulta numa aplicação de função que não será executada, mesmo que as definições sejam adicionadas mais tarde.

## <a name="storage-account-credentials-invalid"></a>Credenciais da conta de armazenamento inválidas

As cadeias de conexão da conta de armazenamento acima devem ser atualizadas se você regenerar as chaves de armazenamento. [Leia mais sobre a gestão da chave](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)de armazenamento aqui .

## <a name="storage-account-inaccessible"></a>Conta de armazenamento inacessível

A sua aplicação de funções deve poder aceder à conta de armazenamento. Os problemas comuns que bloqueiam o acesso de funções a uma conta de armazenamento são:

+ aplicações de função implementadas para App Service Environments (ASE) sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

+ A firewall da conta de armazenamento está ativada e não está configurada para permitir o tráfego de e para funções. Para saber mais, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Quota de execução diária cheia

Se tiver uma quota de execução diária configurada, a sua aplicação de função está temporariamente desativada, o que faz com que muitos dos controlos do portal fiquem indisponíveis. 

+ Para verificar no [portal Azure,](https://portal.azure.com)abra **as funcionalidades** da plataforma > Definições de aplicações de **função** na sua aplicação de função. Quando estiver sobre a Quota de **Utilização Diária** que definiu, verá a seguinte mensagem:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Para resolver este problema, remova ou aumente a quota diária e reinicie a sua aplicação. Caso contrário, a execução da sua aplicação está bloqueada até ao dia seguinte.

## <a name="app-is-behind-a-firewall"></a>O aplicativo está protegido por um firewall

O seu tempo de funcionamento será inalcançável se a sua aplicação de funções estiver alojada num Ambiente de Serviço de [Aplicações equilibrado internamente](../app-service/environment/create-ilb-ase.md) e estiver configurado para bloquear o tráfego de internet de entrada, ou tiver [restrições IP](functions-networking-options.md#inbound-ip-restrictions) de entrada configuradas para bloquear o acesso à Internet. O portal do Azure faz chamadas diretamente para o aplicativo em execução para buscar a lista de funções e também faz chamadas HTTP para o ponto de extremidade KUDU. As definições de nível da plataforma sob o separador `Platform Features` ainda estarão disponíveis.

Para verificar a configuração do ASE, navegue até o NSG da sub-rede em que o ASE reside e valide as regras de entrada para permitir o tráfego proveniente do IP público do computador em que você está acessando o aplicativo. Você também pode usar o portal de um computador conectado à rede virtual que executa seu aplicativo ou uma máquina virtual em execução em sua rede virtual. [Leia mais sobre a configuração da regra de entrada aqui](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a monitorização das suas aplicações de função:

> [!div class="nextstepaction"]
> [Monitorizar funções azure](functions-monitoring.md)

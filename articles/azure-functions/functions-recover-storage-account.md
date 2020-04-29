---
title: 'Erro de resolução de problemas: Funções Azure O tempo de funcionamento é inacessível'
description: Aprenda a resolver problemas com uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063786"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Erro de resolução de problemas: "Funções Azure O tempo de funcionamento é inacessível"

Este artigo ajuda-o a resolver a seguinte cadeia de erros que aparece no portal Azure:

> "Erro: Funções Azure O tempo de funcionamento é inacessível. Clique aqui para mais detalhes sobre a configuração do armazenamento."

Este problema ocorre quando o Tempo de Funcionamento das Funções Azure não pode começar. A razão mais comum para o problema é que a aplicação de funções perdeu acesso à sua conta de armazenamento. Para mais informações, consulte os requisitos da [conta de armazenamento.](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

O resto deste artigo ajuda-o a resolver as seguintes causas deste erro, incluindo como identificar e resolver cada caso.

## <a name="storage-account-was-deleted"></a>Conta de armazenamento foi eliminada

Todas as aplicações de função requerem uma conta de armazenamento para funcionar. Se essa conta for eliminada, a sua função não funcionará.

Comece por procurar o nome da sua conta de armazenamento nas definições da sua aplicação. Ou `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` ou contém o nome da sua conta de armazenamento embrulhada numa cadeia de ligação. Para mais informações, consulte a referência de definições da [App para funções azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Procure a sua conta de armazenamento no portal Azure para ver se ainda existe. Se tiver sido eliminada, recrie a conta de armazenamento e substitua as cordas de ligação de armazenamento. O teu código de função perdeu-se e tens de o reimplantar.

## <a name="storage-account-application-settings-were-deleted"></a>As definições de aplicação da conta de armazenamento foram eliminadas

No passo anterior, se não encontrar uma cadeia de ligação à conta de armazenamento, provavelmente foi apagada ou substituída. A desposição das definições de aplicações ocorre mais frequentemente quando se está a utilizar slots de implementação ou scripts do Gestor de Recursos Do Azure para definir as definições de aplicações.

### <a name="required-application-settings"></a>Definições de aplicação necessárias

* Necessário:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para as funções do plano de consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Para mais informações, consulte a referência de definições da [App para funções azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Orientação

* Não verifique "configuração de slot" para qualquer destas definições. Se trocar as ranhuras de implementação, a aplicação de função quebra-se.
* Não modifique estas definições como parte de implementações automatizadas.
* Estas definições devem ser fornecidas e válidas no momento da criação. Uma implementação automatizada que não contenha estas definições resulta numa aplicação de função que não será executada, mesmo que as definições sejam adicionadas mais tarde.

## <a name="storage-account-credentials-are-invalid"></a>Credenciais de conta de armazenamento são inválidas

As cadeias de ligação à conta de armazenamento previamente discutidas devem ser atualizadas se regenerar as chaves de armazenamento. Para obter mais informações sobre a gestão da chave de armazenamento, consulte [Criar uma conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>Conta de armazenamento é inacessível

A sua aplicação de funções deve poder aceder à conta de armazenamento. Questões comuns que bloqueiam o acesso de uma aplicação de função a uma conta de armazenamento são:

* A aplicação de funções é implementada para o seu App Service Environment sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

* A firewall da conta de armazenamento está ativada e não está configurada para permitir o tráfego de e para funções. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A quota diária de execução está cheia

Se tiver uma quota de execução diária configurada, a sua aplicação de função está temporariamente desativada, o que faz com que muitos dos controlos do portal fiquem indisponíveis. 

Para verificar a quota no [portal Azure,](https://portal.azure.com)selecione Definições de**aplicações** de **funcionalidades** > da plataforma na sua aplicação de função. Se tiver ultrapassado a Quota de **Utilização Diária** que definiu, a seguinte mensagem é exibida:

  > "A App função atingiu a quota de utilização diária e foi interrompida até às próximas 24 horas."

Para resolver este problema, remova ou aumente a quota diária e, em seguida, reinicie a sua aplicação. Caso contrário, a execução da sua aplicação está bloqueada até ao dia seguinte.

## <a name="app-is-behind-a-firewall"></a>App está por trás de uma firewall

O seu tempo de funcionamento pode ser inacessível por qualquer uma das seguintes razões:

* A sua aplicação de funções está hospedada num Ambiente de Serviço de [Aplicações equilibrado internamente](../app-service/environment/create-ilb-ase.md) e está configurada para bloquear o tráfego de internet de entrada.

* A sua aplicação de funções tem [restrições IP de entrada](functions-networking-options.md#inbound-ip-restrictions) configuradas para bloquear o acesso à Internet. 

O portal Azure faz chamadas diretamente para a app de execução para obter a lista de funções, e faz chamadas HTTP para o ponto final de Kudu. As definições ao nível da plataforma no separador **Funcionalidades** da Plataforma ainda estão disponíveis.

Para verificar a configuração do ambiente do serviço de aplicações:
1. Vá ao grupo de segurança da rede (NSG) da subnet onde reside o Ambiente do Serviço de Aplicações.
1. Valide as regras de entrada para permitir o tráfego que vem do IP público do computador onde acede à aplicação. 
   
Também pode utilizar o portal a partir de um computador que está ligado à rede virtual que está a executar a sua aplicação ou a uma máquina virtual que está a funcionar na sua rede virtual. 

Para obter mais informações sobre a configuração das regras de entrada, consulte a secção "Network Security Groups" das considerações de [Networking para um Ambiente](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)de Serviço de Aplicações .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a monitorização das suas aplicações de função:

> [!div class="nextstepaction"]
> [Monitorizar as Funções do Azure](functions-monitoring.md)

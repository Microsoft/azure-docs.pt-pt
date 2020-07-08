---
title: 'Erro de resolução de problemas: O tempo de execução das funções Azure é inacessível'
description: Saiba como resolver problemas com uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063786"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Erro de resolução de problemas: "O tempo de execução das funções Azure é inacessível"

Este artigo ajuda-o a resolver os problemas da seguinte cadeia de erro que aparece no portal Azure:

> "Erro: O tempo de execução das funções Azure é inacessível. Clique aqui para mais detalhes sobre a configuração do armazenamento."

Este problema ocorre quando o tempo de execução das funções Azure não pode começar. A razão mais comum para o problema é que a aplicação de função perdeu o acesso à sua conta de armazenamento. Para obter mais informações, consulte os [requisitos da conta de Armazenamento.](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

O resto deste artigo ajuda-o a resolver os seguintes problemas deste erro, incluindo como identificar e resolver cada caso.

## <a name="storage-account-was-deleted"></a>A conta de armazenamento foi eliminada

Cada aplicação de função requer que uma conta de armazenamento funcione. Se essa conta for apagada, a sua função não funcionará.

Comece por procurar o nome da sua conta de armazenamento nas definições da sua aplicação. Ou `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contém o nome da sua conta de armazenamento embrulhada numa cadeia de ligação. Para obter mais informações, consulte [a referência de definições de aplicações para Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Procure a sua conta de armazenamento no portal Azure para ver se ainda existe. Se tiver sido eliminada, volte a criar a conta de armazenamento e substitua as cordas de ligação de armazenamento. O seu código de função está perdido e precisa de o recolocar.

## <a name="storage-account-application-settings-were-deleted"></a>As definições de pedido de conta de armazenamento foram eliminadas

No passo anterior, se não encontrar uma cadeia de ligação de conta de armazenamento, provavelmente foi apagada ou substituída. Eliminar as definições de aplicações ocorre mais frequentemente quando se está a usar slots de implementação ou scripts do Gestor de Recursos Azure para definir as definições de aplicações.

### <a name="required-application-settings"></a>Definições de aplicação necessárias

* Necessário:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Requerido para funções de plano de consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Para obter mais informações, consulte [a referência de definições de aplicações para Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Orientação

* Não verifique a "definição de ranhura" para nenhuma destas definições. Se trocar slots de implementação, a aplicação de função quebra.
* Não modifique estas definições como parte de implementações automatizadas.
* Estas definições devem ser fornecidas e válidas no momento da criação. Uma implementação automatizada que não contenha estas definições resulta numa aplicação de função que não será executada, mesmo que as definições sejam adicionadas mais tarde.

## <a name="storage-account-credentials-are-invalid"></a>As credenciais de conta de armazenamento são inválidas

As cadeias de ligação da conta de armazenamento previamente discutidas devem ser atualizadas se regenerar as chaves de armazenamento. Para obter mais informações sobre a gestão das chaves de armazenamento, consulte [Criar uma conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>A conta de armazenamento é inacessível

A aplicação de função deve poder aceder à conta de armazenamento. Questões comuns que bloqueiam o acesso de uma aplicação de função a uma conta de armazenamento são:

* A aplicação de função é implantada no seu Ambiente de Serviço de Aplicações sem as regras corretas de rede para permitir o tráfego de e para a conta de armazenamento.

* A firewall da conta de armazenamento está ativada e não configurada para permitir o tráfego de e para funções. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A quota de execução diária está cheia

Se tiver uma quota de execução diária configurada, a sua aplicação de função é temporariamente desativada, o que faz com que muitos dos controlos do portal fiquem indisponíveis. 

Para verificar a quota no [portal Azure,](https://portal.azure.com)selecione **Configurações de**  >  **Aplicações de Função** de Funcionalidades de Plataforma na sua aplicação de função. Se tiver ultrapassado a **quota de utilização diária** que definiu, é apresentada a seguinte mensagem:

  > "A App de Função atingiu a quota de utilização diária e foi interrompida até ao período de 24 horas seguintes."

Para resolver este problema, remova ou aumente a quota diária e, em seguida, reinicie a sua app. Caso contrário, a execução da sua aplicação está bloqueada até ao dia seguinte.

## <a name="app-is-behind-a-firewall"></a>App está por trás de uma firewall

O tempo de funcionamento da sua função pode ser inacessível por uma das seguintes razões:

* A sua aplicação de função está hospedada num [Ambiente de Serviço de Aplicações equilibrado internamente](../app-service/environment/create-ilb-ase.md) e está configurada para bloquear o tráfego de internet de entrada.

* A sua aplicação de função tem [restrições IP de entrada](functions-networking-options.md#inbound-ip-restrictions) que estão configuradas para bloquear o acesso à Internet. 

O portal Azure faz chamadas diretamente para a aplicação de execução para recolher a lista de funções, e faz chamadas HTTP para o ponto final de Kudu. As definições ao nível da plataforma no separador **Funcionalidades** da Plataforma ainda estão disponíveis.

Para verificar a configuração do Ambiente do Serviço de Aplicações:
1. Vá ao grupo de segurança da rede (NSG) da sub-rede onde reside o App Service Environment.
1. Valide as regras de entrada para permitir o tráfego que vem do IP público do computador onde está a aceder à aplicação. 
   
Também pode utilizar o portal a partir de um computador que está ligado à rede virtual que está a executar a sua aplicação ou a uma máquina virtual que está a funcionar na sua rede virtual. 

Para obter mais informações sobre a configuração da regra de entrada, consulte a secção "Grupos de Segurança da Rede" de considerações de [Networking para um Ambiente de Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Próximos passos

Saiba como monitorizar as suas aplicações de função:

> [!div class="nextstepaction"]
> [Monitorizar as Funções do Azure](functions-monitoring.md)

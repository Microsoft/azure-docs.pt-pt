---
title: Cloud Shell em uma rede virtual Azure
description: Implementar a Cloud Shell numa rede virtual Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222807"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Implementar a Cloud Shell numa rede virtual Azure

Uma sessão regular da Cloud Shell funciona num contentor numa rede da Microsoft separada dos seus recursos. Isto significa que os comandos que correm dentro do contentor não podem aceder a recursos que só podem ser acedidos a partir de uma rede virtual específica. Por exemplo, não é possível utilizar o SSH para ligar da Cloud Shell a uma máquina virtual que tenha apenas um endereço IP privado, ou utilizar kubectl para ligar a um cluster Kubernetes que bloqueou o acesso. 

Esta funcionalidade opcional aborda estas limitações e permite-lhe implantar a Cloud Shell numa rede virtual Azure que controla. A partir daí, o contentor é capaz de interagir com recursos dentro da rede virtual que seleciona.  

Abaixo pode ver a arquitetura de recursos que será implantada e usada neste cenário.

![Ilustra a arquitetura isolada da Cloud Shell VNET.](media/private-vnet/data-diagram.png)

Antes de utilizar o Cloud Shell na sua própria Rede Virtual Azure, terá de criar vários recursos para suportar esta funcionalidade. Este artigo mostra como configurar os recursos necessários usando um modelo ARM.

> [!NOTE]
> Estes recursos só precisam de ser criados uma vez para a rede virtual. Podem então ser partilhados por todos os administradores com acesso à rede virtual.

## <a name="required-network-resources"></a>Recursos de rede necessários

### <a name="virtual-network"></a>Rede virtual
Uma rede virtual define o espaço de endereço no qual uma ou mais sub-redes são criadas.

A rede virtual desejada para ser usada para cloud Shell precisa de ser identificada. Esta será geralmente uma rede virtual existente que contém recursos que gostaria de gerir ou uma rede que pares com redes que contenham os seus recursos.

### <a name="subnet"></a>Sub-rede
Dentro da rede virtual selecionada, uma sub-rede dedicada deve ser utilizada para recipientes Cloud Shell. Esta sub-rede é delegada no serviço Azure Container Instances (ACI).  Quando um utilizador solicita um recipiente Cloud Shell numa rede virtual, a Cloud Shell utiliza o ACI para criar um contentor que se encontra nesta sub-rede delegada.  Não podem ser criados outros recursos nesta sub-rede.

### <a name="network-profile"></a>Perfil de rede
Um perfil de rede é um modelo de configuração de rede para recursos Azure que especifica certas propriedades de rede para o recurso.

### <a name="azure-relay"></a>Reencaminhamento do Azure
Um [Relé Azure](../azure-relay/relay-what-is-it.md) permite dois pontos finais que não são diretamente alcançáveis para comunicar. Neste caso, é utilizado para permitir que o navegador do administrador comunique com o contentor na rede privada.

A instância Azure Relay utilizada para cloud Shell pode ser configurada para controlar quais as redes que podem aceder aos recursos dos contentores: 
- Acessível a partir da internet pública: Nesta configuração, a Cloud Shell fornece uma forma de alcançar recursos internos de outra forma a partir de fora. 
- Acessível a partir de redes especificadas: Nesta configuração, os administradores terão de aceder ao portal Azure a partir de um computador que está a funcionar na rede apropriada para poderem utilizar o Cloud Shell.

## <a name="storage-requirements"></a>Requisitos de armazenamento
Tal como na Cloud Shell padrão, é necessária uma conta de armazenamento durante a utilização da Cloud Shell numa rede virtual. Cada administrador precisa de uma partilha de ficheiros para armazenar os seus ficheiros.  A conta de armazenamento tem de ser acessível a partir da rede virtual que é usada pela Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Limitações de implementação de rede virtual
* Devido aos recursos adicionais de networking envolvidos, iniciar a Cloud Shell numa rede virtual é tipicamente mais lento do que uma sessão padrão da Cloud Shell.

* Todas as regiões cloud shell, para além da Índia Central, são atualmente apoiadas. 

* [A Azure Relay](../azure-relay/relay-what-is-it.md) não é um serviço gratuito, por favor veja os seus [preços.](https://azure.microsoft.com/pricing/details/service-bus/) No cenário Cloud Shell, uma ligação híbrida é usada para cada administrador enquanto estão a usar a Cloud Shell. A ligação será automaticamente desligada após a sessão cloud Shell estar completa.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

O fornecedor de recursos Microsoft.ContainerInstances precisa de ser registado na subscrição que detém a rede virtual que pretende utilizar. Selecione a subscrição adequada com `Set-AzContext -Subscription {subscriptionName}` , e, em seguida, executar:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Se **o Estado de Registo** `Registered` for, não é necessária qualquer ação. Se `NotRegistered` for, `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` corra. 

## <a name="deploy-network-resources"></a>Implementar recursos de rede
 
### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual
Se já tiver um VNET desejado a que gostaria de se ligar, salte esta secção.

No portal Azure, ou utilizando o Azure CLI, a Azure PowerShell, etc. criar um grupo de recursos e uma rede virtual no novo grupo de recursos, **o grupo de recursos e a rede virtual precisam de estar na mesma região.**

### <a name="arm-templates"></a>Modelos de ARM
Utilize o [modelo Azure Quickstart](https://aka.ms/cloudshell/docs/vnet/template) para criar recursos cloud shell numa rede virtual e o [Modelo Azure Quickstart](https://aka.ms/cloudshell/docs/vnet/template/storage) para criar o armazenamento necessário. Tome nota dos seus nomes de recursos, principalmente o nome da sua partilha de ficheiros.

### <a name="open-relay-firewall"></a>Firewall de retransmissão aberta
Navegue para o relé criado usando o modelo acima, selecione "Networking" em definições, permita o acesso da rede do seu navegador ao relé. Por predefinição, o relé só está acessível a partir da rede virtual em que foi criado. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Configurar a Cloud Shell para utilizar uma rede virtual.
> [!NOTE]
> Este passo deve ser concluído para cada administrador utilizar a Cloud Shell.

Depois de ter concluído os passos acima, navegue até cloud Shell no portal Azure ou em https://shell.azure.com . Uma destas experiências deve ser usada sempre que quiser ligar-se a uma experiência isolada da Cloud Shell.

> [!NOTE]
> Se a Cloud Shell foi utilizada no passado, a nuvem existente deve ser desmontada. Para fazer esta corrida `clouddrive unmount` a partir de uma sessão de Cloud Shell ativa, refresque a sua página.

Ligue-se à Cloud Shell, será solicitado com a primeira experiência de execução. Selecione a sua experiência de concha preferida, selecione "Mostrar definições avançadas" e selecione a caixa de isolamento "Mostrar definições de isolamento VNET". Preencha os campos do pop-up.  A maioria dos campos preencherá automaticamente os recursos disponíveis que podem ser associados à Cloud Shell numa rede virtual.  O nome 'Partilha de Ficheiros' terá de ser preenchido pelo utilizador.


![Ilustra as definições de experiência isoladas do Cloud Shell.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md)

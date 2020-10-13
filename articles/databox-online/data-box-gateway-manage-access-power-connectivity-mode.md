---
title: Acesso ao dispositivo Azure Data Box Gateway, potência e conectividade
description: Descreve como gerir o modo de acesso, energia e conectividade para o dispositivo Azure Data Box Gateway que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 27b6d8ca61ed10b5c7362e089fe94d8d64164878
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743867"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerir o modo de acesso, energia e conectividade para o seu Gateway Azure Data Box

Este artigo descreve como gerir o modo de acesso, energia e conectividade para o seu Gateway Azure Data Box. Estas operações são realizadas através da UI web local ou do portal Azure. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Gerir o acesso ao dispositivo
> * Gerir o modo de conectividade
> * Gerir o poder

## <a name="manage-device-access"></a>Gerir o acesso ao dispositivo

O acesso ao seu dispositivo Data Box Gateway é controlado através da utilização de uma palavra-passe do dispositivo. Pode alterar a palavra-passe através da UI web local. Também pode redefinir a palavra-passe do dispositivo no portal Azure.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estes passos na UI local para alterar a palavra-passe do dispositivo.

1. Na uI web local, vá à **Manutenção > alteração de senha**.
2. Introduza a palavra-passe atual e, em seguida, a nova senha. A palavra-passe fornecida deve ter entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Confirme a nova senha.

    ![Alterar palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique **em Alterar palavra-passe**.
 
### <a name="reset-device-password"></a>Redefinir a palavra-passe do dispositivo

O fluxo de trabalho de reset não requer que o utilizador relembre a senha antiga e é útil quando a palavra-passe é perdida. Este fluxo de trabalho é realizado no portal Azure.

1. No portal Azure, **aceda à visão geral > redefinir a palavra-passe de administração**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Introduza a nova senha e, em seguida, confirme-a. A palavra-passe fornecida deve ter entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Clique **em Reiniciar**.

    ![Redefinir a palavra-passe 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerir o acesso a recursos

Para criar o seu recurso Azure Stack Edge Pro/Data Box Gateway, IoT Hub e Azure Storage, necessita de permissões como contribuinte ou superior a um nível de grupo de recursos. Também precisa que os fornecedores de recursos correspondentes estejam registados. Para quaisquer operações que envolvam chave de ativação e credenciais, também são necessárias permissões para a Azure Ative Directory Graph API. Estas são descritas nas seguintes secções.

### <a name="manage-microsoft-graph-api-permissions"></a>Gerir permissões de API do Microsoft Graph

Ao gerar a chave de ativação para o dispositivo Azure Stack Edge Pro, ou realizar quaisquer operações que exijam credenciais, necessita de permissões para a Microsoft Graph API. As operações que precisam de credenciais podem ser:

-  Criar uma parte com uma conta de armazenamento associada.
-  Criar um utilizador que possa aceder às ações do dispositivo.

Você deve ter `User` acesso a inquilino ative directory como você precisa ser capaz de `Read all directory objects` . Não pode ser um utilizador convidado, pois não tem permissões para `Read all directory objects` . Se é um hóspede, então as operações como a geração de uma chave de ativação, a criação de uma parte no seu dispositivo Azure Stack Edge Pro, a criação de um utilizador falhará.

Para obter mais informações sobre como fornecer acesso aos utilizadores à Microsoft Graph API, consulte a [referência de permissões do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registar fornecedores de recursos

Para obter um recurso em Azure (no modelo Azure Resource Manager), precisa de um fornecedor de recursos que suporte a criação desse recurso. Por exemplo, para disponibilizar uma máquina virtual, deverá ter um fornecedor de recursos 'Microsoft.Compute' disponível na subscrição.
 
Os fornecedores de recursos estão registados ao nível da subscrição. Por predefinição, qualquer subscrição nova do Azure é previamente registada com uma lista de fornecedores mais utilizados. O fornecedor de recursos de 'Microsoft.DataBoxEdge' não está incluído nesta lista.

Não é necessário conceder permissões de acesso ao nível de subscrição para que os utilizadores possam criar recursos como o 'Microsoft.DataBoxEdge' dentro dos seus grupos de recursos em que têm direitos de proprietário, desde que os fornecedores de recursos para estes recursos já estejam registados.

Antes de tentar criar qualquer recurso, certifique-se de que o fornecedor de recursos está registado na subscrição. Se o fornecedor de recursos não estiver registado, terá de se certificar de que o utilizador que cria o novo recurso tem direitos suficientes para registar o fornecedor de recursos necessário no nível de subscrição. Se ainda não fez isto, verá o seguinte erro:

*A subscrição \<Subscription name> não tem permissões para registar o(s) fornecedor de recursos: Microsoft.DataBoxEdge.*


Para obter uma lista de fornecedores de recursos registados na subscrição atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para o dispositivo Azure Stack Edge Pro, `Microsoft.DataBoxEdge` deve ser registado. Para se `Microsoft.DataBoxEdge` registar, o administrador de subscrição deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registar um fornecedor de recursos, consulte [Resolver os erros do registo do fornecedor de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="manage-connectivity-mode"></a>Gerir o modo de conectividade

Para além do modo normal padrão, o seu dispositivo também pode funcionar em modo parcialmente desligado ou desligado. Cada um destes modos é descrito como abaixo:

- **Parcialmente desligado** – Neste modo, o dispositivo não consegue enviar quaisquer dados para as ações, no entanto pode ser gerido através do portal Azure.

    Este modo é normalmente utilizado quando numa rede de satélite medido e o objetivo é minimizar o consumo de largura de banda de rede. O consumo mínimo da rede pode ainda ocorrer para operações de monitorização do dispositivo.

- **Desligado** – Neste modo, o dispositivo encontra-se totalmente desligado da nuvem e tanto os uploads e downloads na nuvem estão desativados. O dispositivo só pode ser gerido através da UI web local.

    Este modo é normalmente utilizado quando pretende desligar o seu dispositivo.

Para alterar o modo dispositivo, siga estes passos:

1. Na UI web local do seu dispositivo, aceda às **definições de Configuração > Cloud**.
2. Desative o **upload da Cloud e faça o download**.
3. Para executar o dispositivo em modo parcialmente desligado, ative **a gestão do portal Azure**.

    ![Modo conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo em modo desligado, desative **a gestão do portal Azure**. Agora o dispositivo só pode ser gerido através da UI web local.

    ![Modo conectividade 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerir o poder

Pode desligar ou reiniciar o seu dispositivo virtual utilizando a UI web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no anfitrião e, em seguida, no dispositivo. Esta ação minimiza qualquer possibilidade de danos em dados.

1. Na uI web local, vá para as **definições de Manutenção > Energia**.
2. Clique **em Desligar** ou **Reiniciar** dependendo do que pretende fazer.

    ![Definições de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado para confirmação, clique em **Sim** para prosseguir.

> [!NOTE]
> Se desligar o dispositivo virtual, terá de iniciar o dispositivo através da gestão do hipervisor.

---
title: Modo de acesso, potência e conectividade ao dispositivo Azure Data Box Gateway
description: Descreve como gerir o acesso, energia e o modo de conectividade para o dispositivo Azure Data Box Gateway que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474446"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerencie o modo de acesso, potência e conectividade para o seu Portal de Caixa de Dados Azure

Este artigo descreve como gerir o modo de acesso, potência e conectividade para o seu Portal de Dados Azure. Estas operações são realizadas através da UI web local ou do portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir o acesso ao dispositivo
> * Gerir o modo de conectividade
> * Gerir o poder

## <a name="manage-device-access"></a>Gerir o acesso ao dispositivo

O acesso ao seu dispositivo Data Box Gateway é controlado pela utilização de uma palavra-passe do dispositivo. Pode alterar a palavra-passe através da UI web local. Também pode redefinir a palavra-passe do dispositivo no portal Azure.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estes passos na UI local para alterar a senha do dispositivo.

1. Na web local UI, vá à **Manutenção > alteração**de passwords.
2. Introduza a senha atual e, em seguida, a nova senha. A palavra-passe fornecida deve estar entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Confirme a nova senha.

    ![Alterar palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **Alterar a palavra-passe**.
 
### <a name="reset-device-password"></a>Redefinir a palavra-passe do dispositivo

O fluxo de trabalho de reset não requer que o utilizador relembre a palavra-passe antiga e seja útil quando a palavra-passe é perdida. Este fluxo de trabalho é realizado no portal Azure.

1. No portal Azure, vá ao **Overview > Reset palavra-passe de administrador**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Introduza a nova senha e confirme-a. A palavra-passe fornecida deve estar entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Clique em **Redefinir**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerir o acesso a recursos

Para criar o seu Portal de Borda/Caixa de Dados, IoT Hub e recurso de Armazenamento Azure, precisa de permissões como colaborador ou superior a nível de grupo de recursos. Também precisa que os fornecedores de recursos correspondentes sejam registados. Para quaisquer operações que envolvam chave de ativação e credenciais, também são necessárias permissões para a API do Gráfico de Diretório Ativo Azure. Estes são descritos nas seguintes secções.

### <a name="manage-microsoft-graph-api-permissions"></a>Gerir permissões da Microsoft Graph API

Ao gerar a chave de ativação para o dispositivo Data Box Edge, ou realizar quaisquer operações que exijam credenciais, precisa de permissões para a Microsoft Graph API. As operações que precisam de credenciais podem ser:

-  Criar uma parte com uma conta de armazenamento associada.
-  Criar um utilizador que possa aceder às ações do dispositivo.

Você deve `User` ter acesso ao inquilino do Diretório `Read all directory objects`Ativo como você precisa ser capaz de . Não pode ser um utilizador convidado, pois não `Read all directory objects`tem permissão para. Se for um convidado, as operações como a geração de uma chave de ativação, a criação de uma partilha no dispositivo Data Box Edge e a criação de um utilizador falharão.

Para obter mais informações sobre como fornecer acesso aos utilizadores à Microsoft Graph API, consulte a referência das [permissões](https://docs.microsoft.com/graph/permissions-reference)do Microsoft Graph .

### <a name="register-resource-providers"></a>Registe os fornecedores de recursos

Para fornecer um recurso em Azure (no modelo Azure Resource Manager), precisa de um fornecedor de recursos que apoie a criação desse recurso. Por exemplo, para fornecer uma máquina virtual, deve ter um fornecedor de recursos 'Microsoft.Compute' disponível na subscrição.
 
Os fornecedores de recursos estão registados ao nível da subscrição. Por predefinição, qualquer subscrição nova do Azure é previamente registada com uma lista de fornecedores mais utilizados. O fornecedor de recursos para 'Microsoft.DataBoxEdge' não está incluído nesta lista.

Não é necessário conceder permissões de acesso ao nível de subscrição para que os utilizadores possam criar recursos como o 'Microsoft.DataBoxEdge' dentro dos seus grupos de recursos sobre os qual têm direitos de proprietário, desde que os fornecedores de recursos para estes recursos já estejam registado.

Antes de tentar criar qualquer recurso, certifique-se de que o fornecedor de recursos está registado na subscrição. Se o fornecedor de recursos não estiver registado, terá de se certificar de que o utilizador que cria o novo recurso tem direitos suficientes para registar o fornecedor de recursos necessário no nível de subscrição. Se ainda não fez isto, verá o seguinte erro:

*O \<nome de subscrição de subscrição> não tem permissões para registar o(s) fornecedor de recursos( Microsoft.DataBoxEdge.*


Para obter uma lista de fornecedores de recursos registados na subscrição atual, executar o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para o dispositivo `Microsoft.DataBoxEdge` Data Box Edge, deve ser registado. Para `Microsoft.DataBoxEdge`se registar, o administrador de subscrição deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registar um fornecedor de recursos, consulte Resolver erros para o registo do [fornecedor de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="manage-connectivity-mode"></a>Gerir o modo de conectividade

Para além do modo normal de predefinição, o seu dispositivo também pode funcionar em modo parcialmente desligado ou desligado. Cada um destes modos é descrito como abaixo:

- **Parcialmente desligado** – Neste modo, o dispositivo não pode enviar nenhum dado para as ações, no entanto pode ser gerido através do portal Azure.

    Este modo é normalmente utilizado quando se encontra numa rede de satélite sumido e o objetivo é minimizar o consumo de largura de banda da rede. O consumo mínimo de rede pode ainda ocorrer para as operações de monitorização do dispositivo.

- **Desligado** – Neste modo, o dispositivo está totalmente desligado da nuvem e tanto os uploads e downloads da nuvem são desativados. O dispositivo só pode ser gerido através da UI web local.

    Este modo é normalmente utilizado quando se pretende desligar o dispositivo.

Para alterar o modo do dispositivo, siga estes passos:

1. Na web uI local do seu dispositivo, vá para **configurações de Configuração > Cloud**.
2. Desative o **upload e o download da Cloud.**
3. Para executar o dispositivo em modo parcialmente desligado, ative a **gestão do portal Azure**.

    ![Modo conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo em modo desligado, desative a gestão do **portal Azure**. Agora o dispositivo só pode ser gerido através da UI web local.

    ![Modo conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerir o poder

Pode desligar ou reiniciar o seu dispositivo virtual utilizando a UI web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no anfitrião e, em seguida, no dispositivo. Esta ação minimiza qualquer possibilidade de corrupção de dados.

1. Na web local UI, vá para **as definições**de Manutenção > Energia .
2. Clique em **Desligar** ou **Reiniciar** dependendo do que pretende fazer.

    ![Definições de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado para confirmação, clique **em Sim** para prosseguir.

> [!NOTE]
> Se desligar o dispositivo virtual, terá de ligar o dispositivo através da gestão do hipervisor.

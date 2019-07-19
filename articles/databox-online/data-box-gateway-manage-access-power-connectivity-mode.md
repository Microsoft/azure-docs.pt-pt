---
title: Microsoft Azure Gateway do Data Box o acesso ao dispositivo, a energia e o modo de conectividade | Microsoft Docs
description: Descreve como gerenciar o acesso, a energia e o modo de conectividade para o Gateway do Azure Data Box dispositivo que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 38662fc8dda935d5f000aee6609fd9b2e42de17f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253159"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerenciar o acesso, a energia e o modo de conectividade para seu Gateway do Azure Data Box

Este artigo descreve como gerenciar o acesso, a energia e o modo de conectividade para seu Gateway do Azure Data Box. Essas operações são executadas por meio da interface do usuário da Web local ou da portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerenciar o acesso ao dispositivo
> * Gerenciar modo de conectividade
> * Gerenciar energia

## <a name="manage-device-access"></a>Gerenciar o acesso ao dispositivo

O acesso ao seu dispositivo de Gateway do Data Box é controlado pelo uso de uma senha de dispositivo. Você pode alterar a senha por meio da interface do usuário da Web local. Você também pode redefinir a senha do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estas etapas na interface do usuário local para alterar a senha do dispositivo.

1. Na interface do usuário da Web local, vá para **manutenção > alteração de senha**.
2. Insira a senha atual e a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter três dos seguintes caracteres: letras maiúsculas, minúsculas, números e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário relembre a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. Na portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Insira a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter três dos seguintes caracteres: letras maiúsculas, minúsculas, números e caracteres especiais. Clique em **Redefinir**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerenciar o acesso aos recursos

Para criar seu Data Box Edge/Gateway do Data Box, o Hub IoT e o recurso de armazenamento do Azure, você precisa de permissões como um colaborador ou superior em um nível de grupo de recursos. Você também precisa que os provedores de recursos correspondentes sejam registrados. Para quaisquer operações que envolvam credenciais e chave de ativação, as permissões para Azure Active Directory API do Graph também são necessárias. Elas são descritas nas seções a seguir.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gerenciar permissões de API de Microsoft Azure Active Directory Graph

Ao gerar a chave de ativação para o dispositivo Data Box Edge ou executar qualquer operação que exija credenciais, você precisa de permissões para Azure Active Directory API do Graph. As operações que precisam de credenciais podem ser:

-  Criando um compartilhamento com uma conta de armazenamento associada.
-  Criar um usuário que pode acessar os compartilhamentos no dispositivo.

Você deve ter `User` acesso ao Active Directory locatário, pois precisa ser capaz de `Read all directory objects`fazer isso. Você não pode ser um usuário convidado, pois não tem permissões `Read all directory objects`para. Se for um convidado, as operações como a geração de uma chave de ativação, a criação de uma partilha no dispositivo Data Box Edge e a criação de um utilizador falharão.

Para obter mais informações sobre como fornecer acesso a usuários para Azure Active Directory API do Graph, consulte [acesso padrão para administradores, usuários e usuários convidados](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Registar fornecedores de recursos

Para provisionar um recurso no Azure (no modelo de Azure Resource Manager), você precisa de um provedor de recursos que ofereça suporte à criação desse recurso. Por exemplo, para provisionar uma máquina virtual, você deve ter um provedor de recursos ' Microsoft. Compute ' disponível na assinatura.
 
Os fornecedores de recursos estão registados ao nível da subscrição. Por predefinição, qualquer subscrição nova do Azure é previamente registada com uma lista de fornecedores mais utilizados. O provedor de recursos para ' Microsoft. DataBoxEdge ' não está incluído nesta lista.

Você não precisa conceder permissões de acesso ao nível de assinatura para que os usuários possam criar recursos como ' Microsoft. DataBoxEdge ' em seus grupos de recursos aos quais eles têm direitos de proprietário, desde que os provedores de recursos para esses recursos já estejam registrados.

Antes de tentar criar qualquer recurso, verifique se o provedor de recursos está registrado na assinatura. Se o provedor de recursos não estiver registrado, você precisará certificar-se de que o usuário que está criando o novo recurso tenha direitos suficientes para registrar o provedor de recursos necessário no nível de assinatura. Se você ainda não fez isso, verá o seguinte erro:

*O nome \<da assinatura de assinatura > não tem permissões para registrar o (s) provedor (es) de recursos: Microsoft.DataBoxEdge.*


Para obter uma lista de provedores de recursos registrados na assinatura atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para data Box Edge dispositivo, `Microsoft.DataBoxEdge` deve ser registrado. Para se `Microsoft.DataBoxEdge`registrar, o administrador de assinatura deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registrar um provedor de recursos, consulte [resolver erros de registro do provedor de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gerenciar modo de conectividade

Além do modo normal padrão, o dispositivo também pode ser executado no modo parcialmente desconectado ou desconectado. Cada um desses modos é descrito como abaixo:

- **Parcialmente desconectado** – nesse modo, o dispositivo não pode carregar nenhum dado para os compartilhamentos, no entanto, pode ser gerenciado por meio do portal do Azure.

    Esse modo é normalmente usado quando em uma rede satélite limitada e o objetivo é minimizar o consumo de largura de banda da rede. O consumo mínimo de rede ainda pode ocorrer para operações de monitoramento de dispositivo.

- **Desconectado** – nesse modo, o dispositivo é totalmente desconectado da nuvem e os carregamentos e downloads na nuvem são desabilitados. O dispositivo só pode ser gerenciado por meio da interface do usuário da Web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo do dispositivo, siga estas etapas:

1. Na interface do usuário da Web local do seu dispositivo, vá para **configuração > configurações de nuvem**.
2. Desabilite o **upload e o download da nuvem**.
3. Para executar o dispositivo no modo parcialmente desconectado, habilite o **Gerenciamento de portal do Azure**.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo no modo desconectado, desabilite o **Gerenciamento de portal do Azure**. Agora, o dispositivo só pode ser gerenciado por meio da interface do usuário da Web local.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerenciar energia

Você pode desligar ou reiniciar o dispositivo virtual usando a interface do usuário da Web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no anfitrião e, em seguida, no dispositivo. Essa ação minimiza qualquer possibilidade de corrupção de dados.

1. Na interface do usuário da Web local, vá para **manutenção > configurações de energia**.
2. Clique em **desligar** ou **reiniciar** , dependendo do que você pretende fazer.

    ![Definições de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando a confirmação for solicitada, clique em **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo virtual, será necessário iniciar o dispositivo por meio do gerenciamento de hipervisor.

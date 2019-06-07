---
title: Acesso de dispositivo do Edge de caixa de dados do Azure, power e modo de conectividade | Documentos da Microsoft
description: Descreve como gerir o acesso, energia e modo de conectividade para o dispositivo de limite de caixa de dados do Azure que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476912"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gerir o acesso, energia e modo de conectividade para sua caixa de dados do Azure Edge

Este artigo descreve como gerir o modo de acesso, energia e conectividade para sua caixa de dados do Azure Edge. Estas operações são efetuadas através da IU da web local ou o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir o acesso do dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar a energia


## <a name="manage-device-access"></a>Gerir o acesso do dispositivo

O acesso ao seu dispositivo Edge de caixa de dados é controlado pelo uso de uma palavra-passe do dispositivo. Pode alterar a palavra-passe através da IU da web local. Também pode repor a palavra-passe do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estes passos na IU do local para alterar a palavra-passe do dispositivo.

1. Na IU da web local, aceda a **manutenção > alteração de palavra-passe**.
2. Introduza a palavra-passe atual e, em seguida, a nova palavra-passe. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Certifique-se a nova palavra-passe.

    ![Alterar palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **alterar palavra-passe**.
 
### <a name="reset-device-password"></a>Repor a palavra-passe do dispositivo

O fluxo de trabalho de reposição não exige que o usuário de recuperar a palavra-passe antiga e é útil quando a palavra-passe é perdida. Este fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, aceda a **descrição geral > Repor a palavra-passe do administrador**.

    ![Repor palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Introduza a palavra-passe nova e, em seguida, confirme-lo. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Selecione **repor**.

    ![Repor palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerir o acesso a recursos

Para criar o Gateway de caixa de Edge/dados de caixa de dados, o IoT Hub e a recursos de armazenamento do Azure, terá permissões como um contribuinte ou superior a um nível de grupo de recursos. Também terá dos fornecedores de recursos correspondente a registar. Para todas as operações que envolvem a chave de ativação e as credenciais, também são necessárias permissões para o Azure Active Directory Graph API. Estas são descritas nas seções a seguir.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Gerir permissões do Microsoft Azure Active Directory Graph API

Ao gerar a chave de ativação para o dispositivo de limite de caixa de dados ou executar quaisquer operações que necessitem de credenciais, precisa de permissões para o Azure Active Directory Graph API. As operações que precisam de credenciais podem ser:

-  Criar uma partilha com uma conta de armazenamento associado.
-  Criar um utilizador a quem pode aceder às partilhas no dispositivo.

Deve ter uma `User` acessar no inquilino do Active Directory, conforme necessário conseguir `Read all directory objects`. Não pode ser um utilizador convidado, pois não têm permissões para `Read all directory objects`. Se for um convidado, em seguida, as operações, tais como a geração de uma chave de ativação, criação de uma partilha no seu dispositivo do Edge de caixa de dados, criação de um utilizador todos falhará.

Para obter mais informações sobre como fornecer acesso aos utilizadores para o Azure Active Directory Graph API, consulte [padrão de acesso para os administradores, utilizadores e os utilizadores convidados](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Registar fornecedores de recursos

Para aprovisionar um recurso no Azure (no modelo do Azure Resource Manager), precisa de um fornecedor de recursos que suporta a criação desse recurso. Por exemplo, para Aprovisionar uma máquina virtual, deve ter um fornecedor de recursos "Microsoft. Compute" disponível na subscrição.
 
Fornecedores de recursos são registrados no nível da subscrição. Por predefinição, a qualquer nova subscrição do Azure é previamente registada com uma lista de fornecedores de recursos comumente usados. O fornecedor de recursos para 'Microsoft.DataBoxEdge' não está incluído nesta lista.

Não tem de conceder permissões de acesso para o nível de subscrição para que os utilizadores possam criar recursos, como "Microsoft.DataBoxEdge", em seus grupos de recursos que têm direitos de proprietário, enquanto os fornecedores de recursos para estes recursos já está registado.

Antes de tentar criar todos os recursos, certifique-se de que o fornecedor de recursos está registado na subscrição. Se não estiver registado o fornecedor de recursos, terá de certificar-se de que o utilizador ao criar o novo recurso tem direitos suficientes para registar o fornecedor de recursos necessária no nível da subscrição. Se ainda não fez isso, bem, em seguida, verá o seguinte erro:

*A subscrição <Subscription name> não tem permissões para registar o provider(s) de recursos: Microsoft.DataBoxEdge.*


Para obter uma lista de fornecedores de recursos registado na subscrição atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para o dispositivo de limite de caixa de dados, `Microsoft.DataBoxEdge` deve ser registado. Para registar `Microsoft.DataBoxEdge`, administrador da subscrição deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registar um fornecedor de recursos, consulte [resolver erros de registo do fornecedor de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Para além do modo predefinido de totalmente conectada, o dispositivo também pode executar no modo parcialmente conectado, ou completamente desligado. Cada um desses modos é descrita abaixo:

- **Totalmente conectada** -este é o modo padrão normal em que o dispositivo funciona. O carregamento de cloud e a transferência de dados está ativado neste modo. Pode utilizar o portal do Azure ou a IU da web local para gerir o dispositivo.

- **Parcialmente desligado** – neste modo, o dispositivo não é possível carregar ou transferir qualquer partilha de dados no entanto podem ser gerenciados através do portal do Azure.

    Este modo é normalmente utilizado quando numa rede com tráfego limitado da satélite e o objetivo é minimizar o consumo de largura de banda de rede. Consumo de rede mínima ainda pode ocorrer para operações de monitorização de dispositivos.

- **Desligado** – neste modo, o dispositivo estiver totalmente desligado da cloud e ambos os cloud carregamentos e transferências estão desativadas. O dispositivo só pode ser gerido através da IU da web local.

    Este modo é normalmente utilizado quando deseja colocar offline o seu dispositivo.

Para alterar o modo do dispositivo, siga estes passos:

1. Na IU da web local do seu dispositivo, aceda a **configuração > definições da Cloud**.
2. Na lista pendente, selecione o modo que deseja operar do dispositivo. Pode selecionar a partir **totalmente conectada**, **parcialmente ligado**, e **totalmente desligado**. Para executar o dispositivo no modo desligado parcialmente, ative **do Azure management portal**.

    ![Modo de conectividade](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gerenciar a energia

Pode encerrar ou reiniciar o dispositivo físico com a IU web local. Nós recomendamos que antes de reiniciar, dar as partilhas offline no servidor de dados e, em seguida, o dispositivo. Esta ação minimiza qualquer possibilidade de Corrupção de dados.

1. Na IU da web local, aceda a **manutenção > definições de energia**.
2. Selecione **encerramento** ou **reiniciar** consoante o que pretende fazer.

    ![Definições de energia](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando lhe for pedida confirmação, selecione **Sim** para continuar.

> [!NOTE]
> Se encerrar o dispositivo físico, terá de enviar o botão de energia no dispositivo para ativá-la.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [gerir partilhas](data-box-edge-manage-shares.md).
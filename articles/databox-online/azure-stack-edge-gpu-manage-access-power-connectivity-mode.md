---
title: Acesso ao dispositivo GPU Azure Stack Edge Pro, modo de potência e conectividade | Microsoft Docs
description: Descreve como gerir o modo de acesso, energia e conectividade para o dispositivo GPU Azure Stack Edge Pro que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443119"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Gerir o modo de acesso, potência e conectividade para o seu GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como gerir o modo de acesso, potência e conectividade para o seu Azure Stack Edge Pro com dispositivo GPU. Estas operações são realizadas através da UI web local ou do portal Azure.

Este artigo aplica-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir o acesso ao dispositivo
> * Gerir o acesso a recursos
> * Gerir o modo de conectividade
> * Gerir o poder


## <a name="manage-device-access"></a>Gerir o acesso ao dispositivo

O acesso ao seu dispositivo Azure Stack Edge Pro é controlado através da utilização de uma palavra-passe do dispositivo. Pode alterar a palavra-passe através da UI web local. Também pode redefinir a palavra-passe do dispositivo no portal Azure.

O acesso aos dados nos discos do dispositivo também é controlado por chaves de encriptação em repouso.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estes passos na UI local para alterar a palavra-passe do dispositivo.

1. Na uI web local, vá à **Manutenção > Password.**
2. Introduza a palavra-passe atual e, em seguida, a nova senha. A palavra-passe fornecida deve ter entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Confirme a nova senha.

    ![Alterar palavra-passe](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **Alterar palavra-passe**.
 
### <a name="reset-device-password"></a>Redefinir a palavra-passe do dispositivo

O fluxo de trabalho de reset não requer que o utilizador relembre a senha antiga e é útil quando a palavra-passe é perdida. Este fluxo de trabalho é realizado no portal Azure.

1. No portal Azure, **aceda à visão geral > redefinir a palavra-passe de administração**.

    ![A screenshot mostra o dispositivo com a palavra-passe do dispositivo Reset selecionada.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Introduza a nova senha e, em seguida, confirme-a. A palavra-passe fornecida deve ter entre 8 e 16 caracteres. A palavra-passe deve ter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais. Selecione **Repor**.

    ![Redefinir a palavra-passe 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Gerir o acesso aos dados do dispositivo

Para os dispositivos Azure Stack Edge Pro R e Azure Stack Edge Mini R, o acesso aos dados do dispositivo é controlado utilizando teclas de encriptação em repouso para as unidades do dispositivo. Depois de configurar com sucesso o dispositivo para encriptação em repouso, a opção de chaves de encriptação em repouso rotativa fica disponível na UI local do dispositivo. 

Esta operação permite-lhe alterar as teclas para volumes BitLocker `HcsData` `HcsInternal` e todas as unidades auto-encriptadas do seu dispositivo.

Siga estes passos para rodar as teclas de encriptação em repouso.

1. Na UI local do dispositivo, vá à página **Get start.** No azulejo **de Segurança,** selecione **Encriptação-em-repouso: Rotativa teclas** opção. Esta opção só está disponível depois de configurar com sucesso as teclas de encriptação no descanso.

    ![Selecione as teclas rotativas para encriptação-em-descanso na página Get start](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Pode utilizar as suas próprias teclas BitLocker ou utilizar as teclas geradas pelo sistema.  

    Para fornecer a sua própria chave, introduza uma cadeia codificada Base-64 de 32 caracteres. A entrada é semelhante à que forneceria quando configurar a encriptação em repouso pela primeira vez.

    ![Traga a sua própria chave de encriptação em repouso](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    Também pode optar por utilizar uma chave gerada pelo sistema.

    ![Utilize a chave de encriptação gerada pelo sistema em repouso](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Selecione **Aplicar**. Os protetores das chaves estão rodados.

    ![Aplique a nova chave de encriptação em repouso](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Quando solicitado para descarregar e guardar o ficheiro chave, selecione **Baixar e continuar**. 

    ![Descarregue e continue o ficheiro chave](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Guarde o `.json` ficheiro chave num local seguro. Este ficheiro é utilizado para facilitar uma potencial recuperação futura do dispositivo.

    ![A screenshot mostra a caixa de diálogo de palavra-passe do dispositivo Reset.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerir o acesso a recursos

Para criar o seu recurso Azure Stack Edge / Data Box Gateway, IoT Hub e Azure Storage, necessita de permissões como contribuinte ou superior a um nível de grupo de recursos. Também precisa que os fornecedores de recursos correspondentes estejam registados. Para quaisquer operações que envolvam chave de ativação e credenciais, também são necessárias permissões para a API do Gráfico microsoft. Estas são descritas nas seguintes secções. 

### <a name="manage-microsoft-graph-api-permissions"></a>Gerir permissões de API do Microsoft Graph

Ao gerar a chave de ativação para o dispositivo Azure Stack Edge Pro, ou realizar quaisquer operações que exijam credenciais, necessita de permissões para a Azure Ative Directory Graph API. As operações que precisam de credenciais podem ser:

-  Criar uma parte com uma conta de armazenamento associada.
-  Criar um utilizador que possa aceder às ações do dispositivo.

Você deve ter `User` acesso a inquilino ative directory como você precisa ser capaz de `Read all directory objects` . Não pode ser um utilizador convidado, pois não tem permissões para `Read all directory objects` . Se é um hóspede, então as operações como a geração de uma chave de ativação, a criação de uma partilha no seu dispositivo Azure Stack Edge Pro, a criação de um utilizador, a configuração do papel de computação Edge, a palavra-passe do dispositivo de reset falharão.

Para obter mais informações sobre como fornecer acesso aos utilizadores à Microsoft Graph API, consulte a [referência de permissões do Microsoft Graph](/graph/permissions-reference).

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

Para obter mais informações sobre como registar um fornecedor de recursos, consulte [Resolver os erros do registo do fornecedor de recursos.](../azure-resource-manager/templates/error-register-resource-provider.md)

## <a name="manage-connectivity-mode"></a>Gerir o modo de conectividade

Além do modo padrão totalmente ligado, o seu dispositivo também pode funcionar em modo parcialmente ligado ou totalmente desligado. Cada um destes modos é descrito como abaixo:

- **Totalmente ligado** - Este é o modo predefinido normal em que o dispositivo funciona. Tanto o upload em nuvem como o download de dados estão ativados neste modo. Pode utilizar o portal Azure ou a UI web local para gerir o dispositivo.

- **Parcialmente desligado** – Neste modo, o dispositivo não consegue carregar ou descarregar quaisquer dados de partilha, no entanto pode ser gerido através do portal Azure.

    Este modo é normalmente utilizado quando numa rede de satélite medido e o objetivo é minimizar o consumo de largura de banda de rede. O consumo mínimo da rede pode ainda ocorrer para operações de monitorização do dispositivo.

- **Desligado** – Neste modo, o dispositivo encontra-se totalmente desligado da nuvem e tanto os uploads e downloads na nuvem estão desativados. O dispositivo só pode ser gerido através da UI web local.

    Este modo é normalmente utilizado quando pretende desligar o seu dispositivo.

Para alterar o modo dispositivo, siga estes passos:

1. Na UI web local do seu dispositivo, vá para **a Configuração > Cloud**.
2. A partir da lista de dropdown, selecione o modo em que pretende operar o dispositivo. Pode selecionar a partir de **Totalmente ligado,** **Parcialmente ligado** e **totalmente desligado**. Para executar o dispositivo em modo parcialmente desligado, ative **a gestão do portal Azure**.

 
## <a name="manage-power"></a>Gerir o poder

Pode encerrar ou reiniciar o dispositivo físico com a IU da Web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no servidor de dados e, em seguida, no dispositivo. Esta ação minimiza qualquer possibilidade de danos em dados.

1. Na uI web local, vá à **Manutenção > Power**.
2. Selecione **Desligar** ou **Reiniciar** dependendo do que pretende fazer.

    ![Definições de energia](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado para confirmação, selecione **Sim** para prosseguir.

> [!NOTE]
> Se desligar o dispositivo físico, terá de carregar no botão de alimentação do dispositivo para o ligar.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [gerir as ações.](azure-stack-edge-manage-shares.md)
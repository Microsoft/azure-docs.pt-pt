---
title: Gerir contas de armazenamento do Azure Stack | Documentos da Microsoft
description: Saiba como localizar, gerir, recuperar e recuperar as contas de armazenamento do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 840445b2c1a28d294027c7fbfda9e6bf6e431f14
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990699"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gerir contas de armazenamento no Azure Stack

Saiba como gerir contas de armazenamento no Azure Stack para encontrar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócio.

## <a name="find-a-storage-account"></a>Localizar uma conta de armazenamento
A lista de contas de armazenamento na região pode ser visualizada no Azure Stack por:

1. Inicie sessão para o [do portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **todos os serviços** > **contas de armazenamento**.

   ![](media/azure-stack-manage-storage-accounts/image4.png)

Por predefinição, são apresentadas as primeiras 10 contas. Pode optar por obter muito mais ao clicar o **carregar mais** link na parte inferior da lista.

OU

Se estiver interessado numa conta de armazenamento específica – pode **filtrar e obter as contas relevantes** apenas.


**Para filtrar contas:**

1. Selecione **filtro** na parte superior do painel.
2. No painel de filtro, ele permite que especifique **nome da conta**, **ID de subscrição**, ou **estado** para ajustar a lista de contas de armazenamento a serem exibidos. Utilize-os conforme apropriado.
3. Selecione **atualização**. A lista deve atualizar-se em conformidade.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Para repor o filtro: selecione **filtro**, limpamos as seleções e atualizar.

A caixa de texto de pesquisa (na parte superior do painel de lista de contas de armazenamento) permite-lhe realçar o texto selecionado na lista de contas. Pode utilizar este quando o nome completo ou o ID não está facilmente disponível.

Pode utilizar aqui o texto livre para ajudar a encontrar a conta que está interessado.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Ver os detalhes de conta
Depois de localizar as contas que estiver interessado numa visualização, pode selecionar a conta específica para ver alguns detalhes. Um novo painel abre-se com os detalhes da conta, tais como: o tipo da conta, hora de criação, localização, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperar uma conta eliminada
Pode estar numa situação em que precisar de recuperar uma conta eliminada.

No Azure Stack, há uma forma simples de fazer isso:

1. Navegue para a lista de contas de armazenamento. Ver localizar uma conta de armazenamento neste artigo para obter mais informações.
2. Localize essa conta específica na lista. Se pretender filtrar.
3. Verifique os *estado* da conta. Deverá indicar **Deleted**.
4. Selecione a conta, que abre o painel de detalhes da conta.
5. Na parte superior deste painel, localize a **recuperar** botão e selecione-o.
6. Selecione **Sim** para confirmar.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A recuperação está agora na *processar... aguarde* para uma indicação de que foi concluída com êxito.
   Também pode selecionar o ícone de "sino", na parte superior do portal para ver as indicações de progresso.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Assim que a conta recuperada é sincronizada com êxito, ele pode ser usado novamente.

### <a name="some-gotchas"></a>Algumas armadilhas
* Sua conta eliminada mostra o estado como **fora de retenção**.
  
  Fora de retenção significa que a conta eliminada excedeu o período de retenção e pode não ser recuperável.
* Sua conta eliminada não mostra a lista de contas.
  
  Conta de poderão não ser apresentados na lista conta quando a conta eliminada já foi coletados pelo lixo. Neste caso, não pode ser recuperado. Ver [recuperar a capacidade](#reclaim) neste artigo.

## <a name="set-the-retention-period"></a>Definir o período de retenção
A definição de período de retenção permite que um operador da cloud especificar um período de tempo em dias (entre 0 e 9999 dias) durante o qual qualquer conta eliminada potencialmente pode ser recuperada. O período de retenção predefinido está definido como 0 dias. Definir o valor como "0" significa que qualquer conta eliminada é imediatamente fora de retenção e marcado para coleta de lixo periódica.

**Para alterar o período de retenção:**

1. Inicie sessão para o [do portal de administração](https://adminportal.local.azurestack.external).
2. Selecione **todos os serviços** > **gestão da região** sob **administração**.
3. Selecione **armazenamento** partir do **fornecedores de recursos** lista.
4. Selecione **definições** na parte superior para abrir o painel de definição.
5. Selecione **configuração** , em seguida, edite o valor do período de retenção.

   Definir o número de dias e, em seguida, guardá-lo.
   
   Este valor é imediatamente em vigor e está definido para a sua região inteira.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperar a capacidade
Um dos efeitos colaterais do tendo um período de retenção é que uma conta eliminada continua a consumir capacidade até que trata de fora do período de retenção. Como um operador da nuvem poderá ter uma forma de recuperar o espaço de conta eliminada, mesmo que o período de retenção ainda não tenha expirado.

Pode recuperar a capacidade com o portal ou PowerShell.

**Para recuperar a capacidade com o portal:**
1. Navegue para o painel de contas de armazenamento. Ver localizar uma conta de armazenamento.
2. Selecione **recuperar espaço** na parte superior do painel.
3. Leia a mensagem e, em seguida, selecione **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Aguarde até ver o ícone de campainha no portal de notificação de êxito.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Atualize a página de contas de armazenamento. As contas de eliminado já não são apresentadas na lista, porque eles tenham sido removidos.

Pode também utilizar o PowerShell explicitamente substituir o período de retenção e recuperar imediatamente a capacidade.

**Para recuperar a capacidade com o PowerShell:**   

1. Confirme que tem o Azure PowerShell instalada e configurada. Caso contrário, utilize as instruções seguintes: 
   * Para instalar a versão mais recente do Azure PowerShell e associá-lo a sua subscrição do Azure, veja [como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para obter mais informações sobre os cmdlets do Azure Resource Manager, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Execute os seguintes cmdlets:

> [!NOTE]  
> Se executar estes cmdlets, eliminar permanentemente a conta e o respetivo conteúdo. Não é recuperável. Utilize esta opção com cuidado.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Para obter mais informações, consulte [documentação do Azure Stack do PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Passos Seguintes

 - Para obter informações sobre o gerenciamento de permissões, consulte [controlo de acesso de Manage Role-Based](azure-stack-manage-permissions.md).
 - Para informações sobre como gerir a capacidade de armazenamento para o Azure Stack, veja [gerir a capacidade de armazenamento para o Azure Stack](azure-stack-manage-storage-shares.md).

---
title: Ligar o Explorador de armazenamento para uma subscrição do Azure Stack ou uma conta de armazenamento | Documentos da Microsoft
description: Saiba como ligar o Explorador de armazenamento a uma subscrição do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 1c59f092957704c44b5cda012aa7c471fdaa3275
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763372"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Ligar o Explorador de armazenamento para uma subscrição do Azure Stack ou uma conta de armazenamento

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Neste artigo, irá aprender a ligar às suas subscrições do Azure Stack e as contas de armazenamento com o Explorador de armazenamento. Explorador de armazenamento do Azure é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do armazenamento do Azure Stack no Windows, macOS e Linux.

> [!NOTE]  
> Existem várias ferramentas disponíveis para mover dados para e do armazenamento do Azure Stack. Para obter mais informações, consulte [transferência de dados de ferramentas para o armazenamento do Azure Stack](azure-stack-storage-transfer.md).

Se ainda não instalou o Explorador de armazenamento ainda, [transfira o Explorador de armazenamento](https://www.storageexplorer.com/) e instalá-lo.

Depois de ligar a uma subscrição do Azure Stack ou uma conta de armazenamento, pode utilizar o [artigos do Explorador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabalhar com os seus dados do Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Preparar para ligar ao Azure Stack

Precisa de acesso direto para o Azure Stack ou uma ligação VPN para o Explorador de armazenamento, aceda à subscrição do Azure Stack. Para saber como configurar uma ligação VPN ao Azure Stack, veja [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Ligar ao Azure Stack com VPN).

Para o Azure Stack Development Kit (ASDK), terá de exportar o certificado de raiz de autoridade do Azure Stack.

> [!Note]  
> Para o ASDK, se estiver a ligar ao seu ASDK através de VPN, não utilize o certificado de raiz (CA.cer) que foi criado durante o processo de configuração VPN.  Isso é um certificado codificado em DER e não permitirá que o Explorador de armazenamento obter as suas subscrições do Azure Stack. Siga os passos abaixo para exportar um certificado com codificação Base 64 para utilizar o Explorador de armazenamento.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportar e, em seguida, importar o certificado do Azure Stack

Exportar e, em seguida, importar o certificado do Azure Stack para o ASDK. Para um sistema integrado, o certificado publicamente está assinado. Portanto, este passo não é necessário, enquanto o sistema integrado de configurar a ligação Explorador de armazenamento ao Azure Stack.

1. Abra `mmc.exe` numa máquina de anfitrião do Azure Stack ou numa máquina local com uma ligação VPN ao Azure Stack. 

2. Na **arquivo**, selecione **Adicionar/Remover Snap-in**e, em seguida, adicione **certificados** gerir **minha conta de usuário**.

3.  Sob **consola de certificação de raiz da consola\certificado (computador Local) \Trusted Raiz Fidedigna\Certificados**. Encontrar **AzureStackSelfSignedRootCert**.

    ![Carregue o certificado de raiz do Azure stack através de mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. O certificado com o botão direito, selecione **todas as tarefas** > **exportar**e, em seguida, siga as instruções para exportar o certificado com **X.509 com codificação Base 64 (. CER)**.

    O certificado exportado será utilizado no próximo passo.

5. Iniciar o Explorador de armazenamento, e se vir a **ligar ao armazenamento do Azure** diálogo caixa, cancelá-lo.

6. Sobre o **edite** menu, aponte para **certificados SSL**e, em seguida, selecione **importar certificados**. Utilize a caixa de diálogo do selecionador de ficheiros para encontrar e abrir o certificado que foi exportado no passo anterior.

    Depois de importar o certificado, lhe for pedido para reiniciar o Explorador de armazenamento.

    ![Importar o certificado para o Explorador de armazenamento](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Depois de reinicia o Explorador de armazenamento, selecione o **edite** menu e certifique-se **Azure Stack de destino** está selecionada. Se não estiver, selecione **Azure Stack de destino**e, em seguida, reinicie o Explorador de armazenamento para que a alteração entre em vigor. Esta configuração é necessária para ser compatível com o seu ambiente do Azure Stack.

    ![Certifique-se de que o Azure Stack de destino está selecionado](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Ligar a uma subscrição do Azure Stack com o Azure AD

Utilize os seguintes passos para ligar o Explorador de armazenamento a uma subscrição do Azure Stack, que pertence a uma conta do Azure Active Directory (Azure AD).

1. No painel esquerdo do Explorador de armazenamento, selecione **gerir contas**. 
    São apresentados todos os a subscrição da Microsoft que iniciou sessão.

2. Para ligar à subscrição do Azure Stack, selecione **adicionar uma conta**.

    ![Adicionar uma conta do Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Na caixa de diálogo de armazenamento do Azure, ligar em **ambiente do Azure**, selecione **Azure** ou **Azure China**, que depende da conta do Azure Stack que está a ser utilizada, selecione **Iniciar sessão** para iniciar sessão com a conta do Azure Stack associada com pelo menos uma subscrição ativa do Azure Stack.

    ![Ligar ao armazenamento do Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Depois de iniciar sessão com êxito numa conta do Azure Stack, o painel esquerdo é preenchido com as subscrições do Azure Stack associadas a essa conta. Selecione as subscrições do Azure Stack com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar ou limpar a caixa de verificação **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições do Azure Stack listadas.)

    ![Selecione as subscrições do Azure Stack depois de preencher a caixa de diálogo de Ambiente de Cloud Personalizado](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure Stack selecionadas.

    ![Lista das contas de armazenamento, incluindo as contas de subscrição do Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Ligar a uma subscrição do Azure Stack com a conta do AD FS

> [!Note]  
> A experiência de início de sessão do serviço federado do Azure (AD FS) suporta o Explorador de armazenamento 1.2.0 ou versões mais recentes com o Azure Stack 1804 ou atualização mais recente.
Utilize os seguintes passos para ligar o Explorador de armazenamento a uma subscrição do Azure Stack que pertence a uma conta do AD FS.

1. Selecione **gerir contas**. O Gerenciador de lista as subscrições da Microsoft que iniciou sessão no.
2. Selecione **adicionar uma conta** para ligar à subscrição do Azure Stack.

    ![Adicionar uma conta](media/azure-stack-storage-connect-se/add-an-account.png)

3. Selecione **Seguinte**. Na caixa de diálogo de armazenamento do Azure, ligar em **ambiente do Azure**, selecione **ambiente personalizado de utilização**, em seguida, clique em **seguinte**.

    ![Ligar ao armazenamento do Azure](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Introduza as informações necessárias do ambiente personalizado do Azure Stack. 

    | Campo | Notas |
    | ---   | ---   |
    | Nome do ambiente | O campo pode ser personalizado por utilizador. |
    | Ponto final do Gestor de recursos do Azure | Os exemplos de pontos finais de recursos do Azure Resource Manager do Kit de desenvolvimento do Azure Stack.<br>Para os operadores: https://adminmanagement.local.azurestack.external <br> Para os utilizadores: https://management.local.azurestack.external |

    Se estiver a trabalhar no Azure Stack sistema integrado e não sabe o ponto final de gestão, entre em contato com o operador.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Selecione **iniciar sessão**, para ligar à conta do Azure Stack que associada a pelo menos uma subscrição ativa do Azure Stack.



6. Selecione as subscrições do Azure Stack que deseja trabalhar com. Selecione **Aplicar**.

    ![Gestão de contas](./media/azure-stack-storage-connect-se/account-management.png)

    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure Stack selecionadas.

    ![Lista de subscrições associadas](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Ligar a uma conta de armazenamento do Azure Stack

Também pode ligar a uma conta de armazenamento do Azure Stack com o nome da conta de armazenamento e o par de chaves.

1. No painel esquerdo do Explorador de armazenamento, selecione gerir contas. São apresentadas todas as contas do Microsoft que iniciou sessão.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Para ligar à subscrição do Azure Stack, selecione **adicionar uma conta**.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Na ligar à caixa de diálogo de armazenamento do Azure, selecione **utilize um nome de conta de armazenamento e a chave**.

4. Introduza o nome da sua conta no **nome da conta**, cole a chave da conta no **chave da conta** caixa de texto, selecione **outros (introduzir abaixo)** no **pontos finais de armazenamento domínio** e introduzir o ponto de final do Azure Stack.

    Um ponto de final do Azure Stack inclui duas partes: o nome de uma região e o domínio do Azure Stack. O Kit de desenvolvimento do Azure Stack, o ponto final predefinido é **local.azurestack.external**. Se não tiver a certeza sobre o ponto final, contacte o administrador da nuvem.

    ![Anexar o nome e a chave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Selecione **Ligar**.
6. Depois da conta de armazenamento é anexada com êxito, a conta de armazenamento é apresentada com (**externa, outros**) anexado ao respetivo nome.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Explorador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [O armazenamento do Azure Stack: diferenças e considerações](azure-stack-acs-differences.md)
* Para saber mais sobre o armazenamento do Azure, veja [introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)

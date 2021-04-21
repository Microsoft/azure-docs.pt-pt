---
title: Início Rápido do Azure - Definir e obter um segredo do Key Vault com o portal do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e obter um segredo do Azure Key Vault com o portal do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ba5b375f75d2655045e62583679839c8dd2a5720
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752449"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com o portal do Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste início rápido, vai criar um cofre de chaves e utilizá-lo para armazenar um segredo. 

Para mais informações sobre, consulte 
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral dos segredos.](about-secrets.md)

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao Azure Key Vault, precisará de uma subscrição do Azure. Se ainda não tiver uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Todo o acesso aos segredos ocorre através do Cofre da Chave Azure. Para este arranque rápido, crie um cofre de chaves utilizando [o portal Azure](../general/quick-create-portal.md) [CLI,](../general/quick-create-cli.md)ou [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, siga os passos:

1. Navegue para o seu novo cofre chave no portal Azure
1. Nas páginas de definições do Cofre de Chaves, selecione **Secrets**.
1. Clique em **Gerar/Importar**.
1. No ecrã **Criar um segredo**, selecione os seguintes valores:
    - **Opções de carregamento**: Manual.
    - **Nome**: Digite um nome para o segredo. O nome secreto deve ser único dentro de um Cofre de Chaves. O nome deve ser uma corda de caracteres 1-127, começando com uma letra e contendo apenas 0-9, a-z, A-Z, e -. Para obter mais informações sobre o nome, consulte [objetos key vault, identificadores e versões](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Valor**: Digite um valor para o segredo. As APIs do Cofre-Chave aceitam e devolvem valores secretos como cordas. 
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Depois de receber a mensagem de que o segredo foi criado com êxito, pode clicar no mesmo na lista. 

Para obter mais informações sobre atributos de segredos, consulte [sobre os segredos do Cofre da Chave Azure](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Recupere um segredo do Cofre de Chaves

Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades do segredo](../media/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "Mostrar Valor Secreto" no painel direito, pode ver o valor oculto. 

![O valor secreto apareceu](../media/quick-create-portal/current-version-shown.png)

Também pode utilizar [O Azure CLI,]()ou [Azure PowerShell]() para recuperar o segredo previamente criado.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

> [!NOTE]
> É importante notar que uma vez que um cofre secreto, chave, certificado ou chave é eliminado, ele permanecerá recuperável por um período configurável de 7 a 90 dias de calendário. Se não for especificada nenhuma configuração, o período de recuperação predefinido será definido para 90 dias. Isto proporciona aos utilizadores tempo suficiente para notar uma eliminação acidental e secreta e responder. Para obter mais informações sobre a eliminação e recuperação de cofres chave e objetos-chave do cofre, consulte [a visão geral do Azure Key Vault](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Ler [Acesso seguro a um cofre de chaves](../general/security-overview.md)
- Ver [utilizar o cofre de chaves com app Service Web App](../general/tutorial-net-create-vault-azure-web-app.md)
- Ver [Use Key Vault com aplicação implantada em VM](../general/tutorial-net-virtual-machine.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
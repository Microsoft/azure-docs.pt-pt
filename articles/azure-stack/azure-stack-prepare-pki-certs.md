---
title: Preparar os certificados de infraestrutura de chave pública do Azure Stack para implementação de sistemas integrados do Azure Stack ou rotação secreta | Documentos da Microsoft
description: Descreve como preparar os certificados PKI de pilha do Azure para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 6e11df8bedb88d3e505b7fa3c55ade13282911a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106496"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Preparar os certificados PKI de pilha do Azure para utilização na implementação ou de rotação

Os ficheiros de certificado [obtido a partir de sua autoridade de certificação à escolha](azure-stack-get-pki-certs.md) tem de ser importados e exportados com propriedades correspondentes a requisitos de certificado do Azure Stack.

## <a name="prepare-certificates-for-deployment"></a>Preparar os certificados para implementação

Utilize estes passos para preparar e validar os certificados PKI de pilha do Azure que serão utilizados para implementar um novo ambiente do Azure Stack ou para efetuar a rotação de segredos num ambiente existente do Azure Stack: 

### <a name="import-the-certificate"></a>Importar o certificado

1. Copiar as versões originais do certificado [obtido a partir de sua autoridade de certificação à escolha](azure-stack-get-pki-certs.md) num diretório no anfitrião de implementação. 
   > [!WARNING]
   > Copie os ficheiros que já foram importados, exportados ou alterados de qualquer forma dos ficheiros fornecido diretamente pela AC.

1. Faça duplo clique no certificado e selecione **instalar certificado** ou **Instalar PFX** dependendo de como o certificado foi entregue da AC.

1. Na **Assistente para importar certificados**, selecione **máquina Local** como a localização de importação. Selecione **Seguinte**. No ecrã seguinte, clique novamente em Avançar.

    ![Localização de importação do computador local](./media/prepare-pki-certs/1.png)

1. Escolher **colocar todos os certificados no seguinte arquivo** e, em seguida, selecione **Enterprise confiar** como a localização. Clique em **OK** para fechar a caixa de diálogo de seleção de arquivo do certificado e, em seguida **próxima**.

   ![Configurar o arquivo de certificados](./media/prepare-pki-certs/3.png)

   a. Se estiver a importar um PFX, ser-lhe-á apresentada uma caixa de diálogo adicional. Sobre o **proteção por chave privada** página, introduza a palavra-passe para os seus ficheiros de certificado e, em seguida, ative o **marcar esta chave como exportável. Isto permite-lhe criar cópias de segurança ou transportar as chaves num momento posterior** opção. Selecione **Seguinte**.

   ![Marcar chave como exportável](./media/prepare-pki-certs/2.png)

1. Clique em Concluir para concluir a importação.

### <a name="export-the-certificate"></a>Exportar o certificado

Abra a consola do Gestor de certificados do MMC e ligue-se para o arquivo de certificados do computador Local.

1. Abra a consola de gestão da Microsoft, no Windows 10 com o botão direito clique no Menu Iniciar e, em seguida, clique em executar. Tipo **mmc** clique em ok.

1. Clique em arquivo, adicionar/remover Snap-In, em seguida, selecione os certificados, clique em Adicionar.

    ![Adicionar Snap-in de certificados](./media/prepare-pki-certs/mmc-2.png)
 
1. Selecione a conta de computador, clique em seguinte, em seguida, selecione o computador Local, em seguida, concluir. Clique em ok para fechar a página Adicionar/Remover Snap-In.

    ![Adicionar Snap-in de certificados](./media/prepare-pki-certs/mmc-3.png)

1. Navegue para certificados > empresarial de confiança > localização do certificado. Certifique-se de que vê o certificado no lado direito.

1. Na consola de Gestor de tarefas barra de certificado, selecione **ações** > **todas as tarefas** > **exportar**. Selecione **Seguinte**.

   > [!NOTE]
   > Consoante o número do Azure Stack os certificados que tem, poderão ter de concluir esse processo mais de uma vez.

1. Selecione **Sim, exportar a chave privada**e, em seguida, clique em **próxima**.

1. Na secção exportar formato de ficheiro:
    
   - Selecione **incluir todos os certificados no certificado, se possível**.  
   - Selecione **exportar todas as propriedades expandidas**.  
   - Selecione **ativar privacidade de certificado**.  
   - Clique em **Seguinte**.  
    
     ![Assistente de exportação de certificado com opções de selecionado](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Selecione **palavra-passe** e fornecer uma palavra-passe para os certificados. Crie uma palavra-passe que cumpra os seguintes requisitos de complexidade de palavra-passe. Um comprimento mínimo de oito caracteres. A palavra-passe contém, pelo menos, três dos seguintes: letras em maiúscula letra, letras minúsculas, números de 0 a 9, caracteres especiais, um caráter alfabético que não é maiúscula nem em minúsculas. Tome nota desta palavra-passe. Irá utilizá-lo como um parâmetro de implementação.

1. Selecione **Seguinte**.

1. Escolha um nome de ficheiro e a localização do ficheiro pfx exportar. Selecione **Seguinte**.

1. Selecione **Concluir**.

## <a name="next-steps"></a>Passos Seguintes

[Validar certificados PKI](azure-stack-validate-pki-certs.md)
---
title: Criar um certificado de secure LDAP para um Azure AD Domain Services gerir domínio | Documentos da Microsoft
description: Criar um certificado de secure LDAP para um Azure AD Domain Services gerir domínio
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2017
ms.author: ergreenl
ms.openlocfilehash: 801ba3b84ba9956ca8d13916ac4d90060a7f4037
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883458"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Criar uma. Ficheiro PFX com o certificado de secure LDAP (LDAPS) para um domínio gerido

## <a name="before-you-begin"></a>Antes de começar
Concluída [tarefa 1: obter um certificado para o LDAP seguro](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2: Exportar o certificado de secure LDAP para um. Ficheiro PFX
Antes de começar esta tarefa, obter o certificado de secure LDAP de uma autoridade de certificação pública ou criar um certificado autoassinado.

Para exportar o certificado LDAPS para um. Ficheiro PFX:

1. Prima a **começar** botão e o tipo **R**. Na **execute** caixa de diálogo, escreva **mmc** e clique em **OK**.

    ![Inicie a consola do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Sobre o **User Account Control** linha de comandos, clique em **Sim** para iniciar o MMC (Console de gerenciamento Microsoft) como administrador.
3. Partir do **arquivo** menu, clique em **Adicionar/Remover Snap-in...** .

    ![Adicionar snap-in do console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Na **adicionar ou Remover Snap-ins** caixa de diálogo, selecione a **certificados** snap-in e clique nas **adicionar >** botão.

    ![Adicionar snap-in Certificados à consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. Na **certificados snap-in** assistente, selecione **conta de computador** e clique em **seguinte**.

    ![Adicionar snap-in de certificados para a conta de computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Sobre o **selecionar computador** , selecione **computador Local: (o computador onde esta consola está a ser executada)** e clique em **concluir**.

    ![Adicionar snap-in de certificados - computador Selecione](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Na **adicionar ou Remover Snap-ins** caixa de diálogo, clique em **OK** para adicionar o snap-in de certificados ao MMC.

    ![Adicionar snap-in de certificados ao MMC - concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Na janela do MMC, clique para expandir **raiz da consola**. Deverá ver o snap-in de certificados carregado. Clique em **certificados (computador Local)** para expandir. Clique para expandir a **pessoais** nó, seguido da **certificados** nó.

    ![Arquivo de certificados pessoais aberto](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Deverá ver o certificado autoassinado que criámos. Pode examinar as propriedades do certificado para verificar que o thumbprint corresponde ao que reportadas no PowerShell windows quando criou o certificado.
10. Selecione o certificado autoassinado e **com o botão direito**. A partir do menu de contexto, selecione **todas as tarefas** e selecione **exportar...** .

    ![Exportar o certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. Na **Assistente para exportar certificados**, clique em **próxima**.

    ![Assistente para exportar certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Sobre o **exportar chave privada** página, selecione **Sim, exportar a chave privada**e clique em **seguinte**.

    ![Exportar chave privada do certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > TEM de exportar a chave privada, juntamente com o certificado. Se fornecer uma PFX que contém a chave privada do certificado, ativar o LDAP seguro para o seu domínio gerido falha.
    >
    >

13. Sobre o **exportar formato de ficheiro** página, selecione **Personal Information Exchange - PKCS #12 (. PFX)** como formato de ficheiro de certificado exportado.

    ![Exportar formato de ficheiro de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Apenas o. Formato de ficheiro PFX é suportado. Não exporte o certificado para o. Formato de ficheiro CER.
    >
    >

14. Sobre o **segurança** página, selecione a **palavra-passe** opção e introduza uma palavra-passe para proteger o. Ficheiro PFX. Lembre-se desta palavra-passe, uma vez que é necessária a seguinte tarefa. Clique em **Seguinte**.

    ![Palavra-passe para exportar certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Tome nota desta palavra-passe. Terá ao ativar o LDAP seguro para este domínio gerido no [tarefa 3 - ativar o LDAP seguro para o domínio gerido](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Sobre o **ficheiro a exportar** , especifique o nome de ficheiro e a localização onde pretende exportar o certificado.

    ![Caminho para exportar certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na página seguinte, clique em **concluir** para exportar o certificado para um ficheiro PFX. Deverá ver a caixa de diálogo de confirmação quando o certificado foi exportado.

    ![Exportar certificado concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Passo seguinte
[Tarefa 3: Ativar o LDAP seguro para o domínio gerido](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

---
title: Instalar o Azure AD Connect com permissões de administrador do SQL delegado | Documentos da Microsoft
description: Este tópico descreve uma atualização para o Azure AD Connect, que permite a instalação utilizando uma conta que tem apenas permissões de dbo do SQL.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092587"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalar o Azure AD Connect com permissões de administrador do SQL delegado
Antes da compilação mais recente do Azure AD Connect, a delegação administrativa, ao implementar as configurações necessárias do SQL, não era suportada.  Os utilizadores que pretendem instalar o Azure AD Connect é necessário ter permissões de administrador (SA) de servidor no SQL server.

Com a versão mais recente do Azure AD Connect, o aprovisionamento da base de dados pode agora ser realizado fora de banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário da base de dados.

## <a name="before-you-begin"></a>Antes de começar
Para utilizar esta funcionalidade, terá de perceber que existem várias partes móveis e cada um deles pode envolver um administrador diferente na sua organização.  A tabela seguinte resume as funções individuais e as suas obrigações respectivas na implantação do Azure AD Connect com esta funcionalidade.

|Função|Descrição|
|-----|-----|
|Domínio ou de administrador do AD de floresta|Cria a conta de serviço de nível de domínio que é utilizada pelo Azure AD Connect para executar o serviço de sincronização.  Para obter mais informações sobre contas de serviço, consulte [contas e permissões](reference-connect-accounts-permissions.md).
|Administrador do SQL|Cria a base de dados ADSync e concede o início de sessão + dbo acesso ao administrador do Azure AD Connect e a conta de serviço criado pelo administrador de domínio/floresta.|
Administrador do Azure AD Connect|Instala o Azure AD Connect e especifica a conta de serviço durante a instalação personalizada.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Permissões delegadas de passos para instalar o Azure AD Connect com o SQL
Para aprovisionar a base de dados fora de banda e instale o Azure AD Connect com permissões de proprietário da base de dados, utilize os seguintes passos.

>[!NOTE]
>Embora não seja necessário, é **vivamente recomendado** que o agrupamento de Latin1_General_CI_AS é selecionado ao criar a base de dados.


1. Que o administrador de SQL, criar a base de dados ADSync com uma sequência de agrupamento não sensível **(Latin1_General_CI_AS)**.  A base de dados deve ser nomeado **ADSync**.  O modelo de recuperação, o nível de compatibilidade e o tipo de contenção são atualizadas para os valores corretos quando o Azure AD Connect está instalado.  No entanto, a sequência de agrupamento tem de ser definida corretamente pelo administrador do SQL do Azure AD Connect irá bloquear a instalação.  Para recuperar o SA tem de eliminar e recriar a base de dados.
 
   ![Agrupamento](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Conceda o administrador do Azure AD Connect e a conta de serviço de domínio as seguintes permissões:
   - Início de sessão do SQL 
   - **base de dados owner(dbo)** direitos.
 
   ![Permissões](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >O Azure AD Connect não suporta a inícios de sessão com uma associação aninhada.  Isso significa que a sua conta de administrador do Azure AD Connect e a conta de serviço de domínio têm de ser associadas a um início de sessão é direitos de dbo.  Simplesmente não pode ser o membro de um grupo que está atribuído a um início de sessão com direitos de dbo.

3. Envie um e-mail para o administrador do Azure AD Connect que indica o nome de servidor e a instância SQL que deve ser utilizado quando instalar o Azure AD Connect.

## <a name="additional-information"></a>Informações adicionais
Assim que a base de dados é aprovisionado, o administrador do Azure AD Connect pode instalar e configurar sincronização no local a suas necessidades.

No caso do administrador do SQL tem restaurada ADSync base de dados a partir de uma cópia de segurança do Azure AD Connect anterior, terá de instalar o novo servidor do Azure AD Connect com a base de dados existente. Para obter mais informações sobre como instalar o Azure AD Connect com uma base de dados existente, consulte [instalar o Azure AD Connect com a base de dados ADSync existente](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Passos Seguintes
- [Introdução ao Azure AD Connect com as definições rápidas](how-to-connect-install-express.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
- [Instalar o Azure AD Connect com uma base de dados ADSync existente](how-to-connect-install-existing-database.md)  

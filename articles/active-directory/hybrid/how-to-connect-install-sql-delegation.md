---
title: Instale o Azure AD Connect utilizando permissões de administrador delegadas da SQL | Microsoft Docs
description: Este tópico descreve uma atualização para Azure AD Connect que permite a instalação usando uma conta que só tem permissões de dbo SQL.
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
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85358672"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalar o Azure AD Connect com permissões de administrador do SQL delegado
Antes da mais recente construção do Azure AD Connect, a delegação administrativa, ao implementar configurações que exigiam SQL, não foi suportada.  Os utilizadores que quisessem instalar o Azure AD Connect precisavam de ter permissões de administrador de servidor (SA) no servidor SQL.

Com a mais recente versão do Azure AD Connect, o fornecimento da base de dados pode agora ser executado fora da banda pelo administrador SQL e depois instalado pelo administrador Azure AD Connect com direitos de proprietário de base de dados.

## <a name="before-you-begin"></a>Antes de começar
Para utilizar esta funcionalidade, é necessário perceber que existem várias peças móveis e cada uma pode envolver um administrador diferente na sua organização.  A tabela seguinte resume as funções individuais e as respetivas funções na implantação do Azure AD Connect com esta funcionalidade.

|Função|Description|
|-----|-----|
|Administrador de AD de domínio ou floresta|Cria a conta de serviço de nível de domínio que é utilizada pelo Azure AD Connect para executar o serviço de sincronização.  Para obter mais informações sobre contas de serviço, consulte [Contas e permissões.](reference-connect-accounts-permissions.md)
|Administrador do SQL|Cria a base de dados ADSync e concede acesso de login + dbo ao administrador Azure AD Connect e à conta de serviço criada pelo administrador de domínio/floresta.|
Administrador AZURE AD Connect|Instala o Azure AD Connect e especifica a conta de serviço durante a instalação personalizada.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Passos para instalar o Azure AD Connect utilizando permissões delegadas do SQL
Para forragem da base de dados fora da banda e instalar o Azure AD Connect com permissões do proprietário da base de dados, utilize os seguintes passos.

>[!NOTE]
>Embora não seja necessário, é **altamente recomendável** que a Latin1_General_CI_AS colagem seja selecionada na criação da base de dados.


1. Que o Administrador SQL crie a base de dados ADSync com uma sequência de colagem insensível **(Latin1_General_CI_AS)**.  A base de dados deve ser denominada **ADSync.**  O modelo de recuperação, o nível de compatibilidade e o tipo de contenção são atualizados para os valores corretos quando o Azure AD Connect é instalado.  No entanto, a sequência de colagem deve ser corretamente definida pelo administrador SQL, caso contrário, o Azure AD Connect bloqueará a instalação.  Para recuperar a SA deve eliminar e recriar a base de dados.
 
   ![Agrupamento](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Conceder ao administrador AZure AD Connect e à conta de serviço de domínio as seguintes permissões:
   - Início de sessão do SQL 
   - **direitos do proprietário da base de dados (dbo).**
 
   ![Permissões](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >O Azure AD Connect não suporta logins com uma filiação aninhada.  Isto significa que a sua conta de administrador AZure AD Connect e conta de serviço de domínio deve estar ligada a um login que é concedido direitos dbo.  Não pode simplesmente ser o membro de um grupo que é atribuído a um login com direitos dbo.

3. Envie um e-mail para o administrador Azure AD Connect indicando o servidor SQL e o nome da instância que deve ser usado ao instalar o Azure AD Connect.

## <a name="additional-information"></a>Informações adicionais
Uma vez que a base de dados é alojada, o administrador Azure AD Connect pode instalar e configurar a sincronização no local à sua conveniência.

Caso o Administrador SQL tenha restaurado a base de dados ADSync a partir de uma cópia de segurança anterior do Azure AD Connect, terá de instalar o novo servidor AZURE AD Connect utilizando uma base de dados existente. Para obter mais informações sobre a instalação do Azure AD Connect com uma base de dados existente, consulte [instalar o Azure AD Connect utilizando uma base de dados ADSync existente.](how-to-connect-install-existing-database.md)

## <a name="next-steps"></a>Passos seguintes
- [Introdução ao Azure AD Connect com as definições rápidas](how-to-connect-install-express.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
- [Instalar o Azure AD Connect com uma base de dados ADSync existente](how-to-connect-install-existing-database.md)  

---
title: Instale o Azure AD Connect utilizando permissões de administrador delegados DaSQL [ Microsoft Docs
description: Este tópico descreve uma atualização do Azure AD Connect que permite a instalação utilizando uma conta que apenas tem permissões SQL dbo.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60243520"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalar o Azure AD Connect com permissões de administrador do SQL delegado
Antes da mais recente construção do Azure AD Connect, a delegação administrativa, ao implementar configurações que exigiam o SQL, não foi apoiada.  Os utilizadores que quisessem instalar o Azure AD Connect precisavam de ter permissões de administrador de servidores (SA) no servidor SQL.

Com o mais recente lançamento do Azure AD Connect, o fornecimento da base de dados pode agora ser realizado fora de banda pelo administrador SQL e, em seguida, instalado pelo administrador Azure AD Connect com direitos de proprietário de base de dados.

## <a name="before-you-begin"></a>Antes de começar
Para utilizar esta funcionalidade, tem de perceber que existem várias partes móveis e cada uma pode envolver um administrador diferente na sua organização.  A tabela seguinte resume as funções individuais e as respetivas funções na implementação do Azure AD Connect com esta funcionalidade.

|Função|Descrição|
|-----|-----|
|Administrador de Anúncios de Domínio ou Florestal|Cria a conta de serviço de nível de domínio que é usada pelo Azure AD Connect para executar o serviço de sincronização.  Para obter mais informações sobre contas de serviço, consulte [Contas e permissões.](reference-connect-accounts-permissions.md)
|Administrador SQL|Cria a base de dados ADSync e concede acesso ao login + dbo ao administrador Azure AD Connect e à conta de serviço criada pelo domínio/administração florestal.|
Administrador azure AD Connect|Instala o Azure AD Connect e especifica a conta de serviço durante a instalação personalizada.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Passos para instalar o Azure AD Connect utilizando permissões delegadas da SQL
Para fornecer a base de dados fora da banda e instalar o Azure AD Connect com permissões do proprietário da base de dados, utilize os seguintes passos.

>[!NOTE]
>Embora não seja necessário, é **altamente recomendável** que a colagem Latin1_General_CI_AS seja selecionada na criação da base de dados.


1. Mande o Administrador SQL criar a base de dados ADSync com uma sequência de colagem insensível **(Latin1_General_CI_AS)**.  A base de dados deve ser denominada **ADSync**.  O modelo de recuperação, o nível de compatibilidade e o tipo de contenção são atualizados para os valores corretos quando o Azure AD Connect está instalado.  No entanto, a sequência de colagem deve ser corretamente definida pelo administrador SQL, caso contrário o Azure AD Connect bloqueia a instalação.  Para recuperar a SA deve eliminar e recriar a base de dados.
 
   ![Agrupamento](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Conceda ao administrador Azure AD Connect e à conta de serviço de domínio as seguintes permissões:
   - Início de sessão do SQL 
   - **direitos do proprietário da base de dados(dbo).**
 
   ![Permissões](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >O Azure AD Connect não suporta logins com uma adesão aninhada.  Isto significa que a sua conta de administrador AD Connect Azur e a conta de serviço de domínio devem estar ligadas a um login que lhe seja concedido direitos dbo.  Não pode ser simplesmente o membro de um grupo que é designado para um login com direitos dbo.

3. Envie um e-mail ao administrador Azure AD Connect indicando o servidor SQL e o nome da instância que deve ser usado na instalação do Azure AD Connect.

## <a name="additional-information"></a>Informações adicionais
Uma vez que a base de dados é aprovisionada, o administrador Azure AD Connect pode instalar e configurar a sincronização no local a seu bel-prazer.

Caso o Administrador SQL tenha restaurado a base de dados ADSync de uma cópia de segurança anterior do Azure AD Connect, terá de instalar o novo servidor Azure AD Connect utilizando uma base de dados existente. Para obter mais informações sobre a instalação do Azure AD Connect com uma base de dados existente, consulte [a Instalação do Azure AD Connect utilizando uma base de dados ADSync existente](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Passos seguintes
- [Introdução ao Azure AD Connect com as definições rápidas](how-to-connect-install-express.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
- [Instalar o Azure AD Connect com uma base de dados ADSync existente](how-to-connect-install-existing-database.md)  

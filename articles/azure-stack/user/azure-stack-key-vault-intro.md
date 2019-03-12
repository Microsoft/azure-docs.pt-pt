---
title: Introdução ao Cofre de chaves de pilha Azure | Documentos da Microsoft
description: Saiba como o Cofre de chaves do Azure Stack gere chaves e segredos
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 87c93f77011082d3e43b1c7d238999441f1b90c1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783011"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução ao Cofre de chaves no Azure Stack

## <a name="prerequisites"></a>Pré-requisitos

* Tem de subscrever uma oferta que inclui o serviço Azure Key Vault.  
* [PowerShell está configurado para utilização com o Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Noções básicas do Key Vault

Cofre de chaves no Azure Stack ajuda a salvaguardar chaves criptográficas e segredos na cloud a aplicações e serviços utilizam. Ao utilizar o Key Vault, pode encriptar chaves e segredos, tais como:

* Chaves de autenticação
* Chaves de contas de armazenamento
* Chaves de encriptação de dados
* ficheiros. pfx
* Palavras-passe

A chave de cofre simplifica o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Os programadores podem criar as chaves de desenvolvimento e teste em minutos e migrá-las totalmente para as chaves de produção. Os administradores de segurança podem conceder (e revogar) permissões para chaves, conforme necessário.

Qualquer pessoa com uma subscrição do Azure Stack pode criar e utilizar cofres de chaves. Embora a chave de cofre beneficiar os programadores e administradores de segurança, o operador que gere outros serviços do Azure Stack para uma organização pode implementar e geri-lo. Por exemplo, o Azure Stack operador pode iniciar a sessão com uma subscrição do Azure Stack, criar um cofre para a organização na qual pretende armazenar chaves e, em seguida, ser responsável por estas tarefas operacionais:

* Criar ou importar uma chave ou segredo.
* Revogar ou eliminar uma chave ou segredo.
* Autorize os utilizadores ou aplicações para aceder ao Cofre de chave, pelo que pode, em seguida, gerir ou utilizar as suas chaves e segredos.
* Configurar a utilização de chave (por exemplo, inicie sessão ou encriptar).

O operador pode, em seguida, fornecer aos desenvolvedores identificadores de recurso uniforme (URIs) para chamar a partir das suas aplicações. Operadores também podem fornecer aos administradores de segurança com informações de registo de utilização de chave.

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o Guia do programador do Cofre de chaves.

## <a name="scenarios"></a>Cenários

Os seguintes cenários descrevem como o Key Vault pode ajudar a satisfazer as necessidades dos desenvolvedores e administradores de segurança.

### <a name="developer-for-an-azure-stack-application"></a>Desenvolvedor para uma aplicação do Azure Stack

**Problema:** Eu quero escrever um aplicativo para o Azure Stack que utilize chaves para assinatura e encriptação. Quero que essas chaves sejam externas à minha aplicação, para que a solução seja adequada a uma aplicação que é distribuída geograficamente.

**Instrução:** As chaves são armazenadas num cofre e invocadas por um URI, quando necessário.

### <a name="developer-for-software-as-a-service-saas"></a>Programador de software como um serviço (SaaS)

**Problema:** Não quero a responsabilidade ou um potencial encargo para chaves e segredos do meu cliente. Quero que clientes sejam proprietário e gerir as chaves, para que eu me possa concentrar no que sei fazer melhor, que é fornecer as principais funcionalidades de software.

**Instrução:** Os clientes podem importar as suas próprias chaves para o Azure Stack e, em seguida, geri-los.

### <a name="chief-security-officer-cso"></a>Diretor de segurança (CSO)

**Problema:** Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas.

**Instrução:** O Key Vault foi concebido para que a Microsoft não veja ou extraia as suas chaves. Quando um aplicativo precisa executar operações de criptografia utilizando as teclas de cliente, o Key Vault utiliza as chaves em nome do aplicativo. A aplicação não consegue ver as chaves de cliente. Embora utilizemos vários serviços do Azure Stack e recursos, pode gerir as chaves a partir de uma localização no Azure Stack. O Cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure Stack, quais são as regiões suporte e quais aplicativos usá-los.

## <a name="next-steps"></a>Passos Seguintes

* [Gerir o Cofre de chaves no Azure Stack pelo portal](azure-stack-key-vault-manage-portal.md)  
* [Gerir o Cofre de chaves no Azure Stack com o PowerShell](azure-stack-key-vault-manage-powershell.md)


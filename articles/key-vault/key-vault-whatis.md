---
title: O que é o cofre de chave do Azure? | Microsoft Docs
description: Saiba como o Azure Key Vault salvaguarda as chaves criptográficas e segredos na cloud a aplicações e serviços utilizam.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 48ac0c3efe74723099e87a77871aa1a78834efbd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958546"
---
# <a name="what-is-azure-key-vault"></a>O que é o cofre de chave do Azure?

Aplicações e serviços cloud, utilize chaves criptográficas e segredos para ajudar a manter as informações seguras. O Azure Key Vault salvaguarda as chaves e segredos. Quando utilizar o Key Vault, pode encriptar chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros. pfx e palavras-passe utilizando chaves protegidas por módulos de segurança de hardware (HSMs).

Key Vault ajuda a resolver os problemas seguintes:

- **Gestão de segredos**: Armazenar de forma segura e totalmente controlar o acesso aos tokens, palavras-passe, certificados, chaves de API e outros segredos.
- **Gestão de chaves**: Criar e controlar as chaves de encriptação que criptografar seus dados. 
- **Gestão de certificados**: Aprovisionar, gerir e implementar certificados de Secure Sockets Layer/Transport Layer Security (SSL/TLS) públicos e privados para utilização com o Azure e seus recursos ligados internos. 
- **Store segredos apoiados por HSMs**: Utilize o software ou o FIPS 140-2 nível 2 validada HSMs para ajudar a proteger chaves e segredos.

## <a name="basic-concepts"></a>Conceitos básicos

O Azure Key Vault é uma ferramenta para armazenar e aceder a segredos em segurança. Um segredo é tudo cujo acesso deseja controlar rigidamente, como chaves de API, palavras-passe ou certificados. Um cofre é um grupo lógico de segredos.

Seguem-se outros termos importantes:

- **Inquilino**: Um inquilino é a organização que possui e gere uma instância específica de serviços cloud da Microsoft. É frequentemente utilizado para referenciar o conjunto de serviços do Azure e o Office 365 para uma organização.

- **Proprietário do cofre**: Um proprietário de cofre pode criar um cofre de chaves e obtenha acesso total e controle sobre ele. O proprietário do cofre também pode configurar uma auditoria ao registo de quem acede a segredos e chaves. Os administradores podem controlar o ciclo de vida das chaves. Podem implementar uma nova versão da chave, fazer uma cópia de segurança e executar tarefas relacionadas.

- **Consumidor de cofre**: Um consumidor de cofre pode executar ações nos recursos no interior do Cofre de chaves, quando o proprietário do cofre concede o acesso de consumidor. As ações disponíveis dependem das permissões concedidas.

- **Recurso**: Um recurso é um item gerível que está disponível através do Azure. Exemplos comuns são a máquina virtual, conta de armazenamento, aplicação web, base de dados e rede virtual. Existem muitos outros.

- **Grupo de recursos**: Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.

- **Principal de serviço**: Um principal de serviço do Azure é uma identidade de segurança criados pelo utilizador a aplicações, serviços e ferramentas de automatização de utilizam para aceder a recursos específicos do Azure. Pense nisso como uma "identidade de utilizador" (nome de utilizador e palavra-passe ou certificado) com uma função específica e permissões controladas de perto. Um principal de serviço apenas precisa de fazer determinadas coisas, ao contrário das identidades de utilizador gerais. Melhora a segurança se conceder apenas o nível de permissão mínimo que precisa para realizar suas tarefas de gestão.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): O Azure AD é o serviço de diretório Active Directory para um inquilino. Cada diretório tem um ou mais domínios. Um diretório pode ter várias subscrições associadas, mas apenas um inquilino.

- **ID de inquilino do Azure**: Um ID de inquilino é uma maneira única para identificar uma instância do Azure AD dentro de uma subscrição do Azure.

- **Gerido identidades**: O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. Utilizar uma identidade gerida faz a resolver esse problema mais simples, fornecendo serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar esta identidade para autenticar no Key Vault ou em qualquer serviço que suporta a autenticação Azure AD, sem ser necessário ter credenciais no seu código. Para obter mais informações, veja a imagem seguinte e o [descrição geral de identidades geridas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagrama de identidades como geridos para o trabalho de recursos do Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Para fazer todas as operações com o Key Vault, primeiro tem de autenticar para o mesmo. Existem três formas de autenticar para o Key Vault:

- [Gerido identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Quando implementa uma aplicação numa máquina virtual no Azure, pode atribuir uma identidade à máquina virtual que tenha acesso ao Cofre de chaves. Também pode atribuir identidades para [outros recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). A vantagem dessa abordagem é que o serviço ou aplicação não está a gerir a rotação do segredo do primeiro. Azure gira automaticamente a identidade. Recomendamos esta abordagem como melhor prática. 
- **Principal e o certificado de serviço**: Pode utilizar um principal de serviço e um certificado associado que tem acesso ao Cofre de chaves. Não recomendamos esta abordagem porque o proprietário da aplicação ou o desenvolvedor deve rodar o certificado.
- **Principal de serviço e o segredo**: Apesar de poder utilizar um principal de serviço e um segredo para autenticar para o Key Vault, não o recomendamos. É difícil girar automaticamente o segredo de arranque de configuração que é utilizado para autenticar para o Key Vault.


## <a name="key-vault-roles"></a>Funções do Key Vault

Utilize a tabela seguinte para melhor compreender como a chave de cofre pode ajudar a satisfazer as necessidades dos programadores e dos administradores de segurança.

| Função | Declaração do problema | Resolvido pelo cofre de chave do Azure |
| --- | --- | --- |
| Programador de uma aplicação do Azure |"Quero criar uma aplicação para o Azure que utilize chaves para assinatura e encriptação. Mas eu quero que essas chaves sejam externas à minha aplicação, para que a solução seja adequada a uma aplicação que é distribuída geograficamente. <br/><br/>Quero que as chaves e os segredos estejam protegidos, sem que tenha de escrever eu próprio o código. E também quero que seja fácil utilizar as chaves e os segredos a partir das minhas aplicações, com um desempenho ótimo”. |As chaves √ são armazenadas num cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware.<br/><br/> √ As chaves são processadas nos HSMs que residem no mesmos datacenters do Azure que as aplicações. Este método proporciona mais fiabilidade e latência reduzida do que as chaves que residem numa localização separada, como no local. |
| Programador de software como um serviço (SaaS) |"Não quero a responsabilidade ou um potencial encargo para chaves e segredos dos inquilino dos meus clientes. <br/><br/>Quero que os clientes sejam proprietários e efetuem a gestão das suas próprias chaves para que eu me possa concentrar no que sei fazer de melhor, ou seja, fornecer as principais funcionalidades de software." |Os clientes √ podem importar as suas próprias chaves para o Azure e geri-las. Quando uma aplicação SaaS tem de efetuar operações de criptografia com chaves dos clientes, o Cofre de chaves faz essas operações em nome do aplicativo. A aplicação não consegue ver as chaves dos clientes. |
| Responsável pela segurança (CSO) |"Quero saber se as nossas aplicações estão em conformidade com os HSMs FIPS 140-2 de nível 2 para uma gestão de chaves segura. <br/><br/>Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas. <br/><br/>E embora utilizemos vários serviços e recursos do Azure, pretendo gerir as chaves a partir de uma única localização no Azure." |Os HSMs √ têm a certificação FIPS 140-2 de nível 2 validada.<br/><br/>A chave do cofre √ foi concebida para que a Microsoft não consulte ou extraia as suas chaves.<br/><br/>√ A utilização da chave é registada em tempo quase real.<br/><br/>√ O cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure, de quantas regiões suporta e quais as aplicações em que os utiliza. |

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chave. Embora a chave de cofre beneficiar os programadores e administradores de segurança, pode ser implementado e gerido pelo administrador de uma organização que gere outros serviços do Azure. Por exemplo, este administrador pode iniciar a sessão com uma subscrição do Azure, crie um cofre para a organização na qual pretende armazenar chaves e, em seguida, ser responsável por tarefas operacionais como esta:

- Criar ou importar uma chave ou segredo
- Revogar ou eliminar uma chave ou segredo
- Autorizar aos utilizadores ou aplicações o acesso à chave de cofre para que possam gerir ou utilizar as suas chaves e segredos
- Configurar a utilização da chave (por exemplo, iniciar sessão ou encriptar)
- Monitorizar a utilização da chave

Este administrador, em seguida, fornece aos desenvolvedores o URIs para chamar a partir das suas aplicações. Este administrador também fornece informações de registo de utilização de chave para o administrador de segurança. 

![Descrição geral de como funciona o Azure Key Vault][1]

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o [manual do programador da chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba como [proteger o seu Cofre](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

---
title: O que é o cofre de chave do Azure? | Microsoft Docs
description: Saiba como o Azure Key Vault salvaguarda chaves criptográficas e segredos que aplicações e serviços em nuvem usam.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 03ad504258dd9448753f37402067a0da3e0a2c00
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197645"
---
# <a name="azure-key-vault-basic-concepts"></a>Conceitos básicos do Cofre chave azure

O Azure Key Vault é uma ferramenta para armazenar e aceder a segredos em segurança. Um segredo é tudo cujo acesso deseja controlar rigidamente, como chaves de API, palavras-passe ou certificados. Um cofre é um grupo lógico de segredos.

Aqui estão outros termos importantes:

- **Inquilino**: um inquilino é a organização que possui e gere uma instância específica de serviços cloud da Microsoft. É mais frequentemente usado para se referir ao conjunto de serviços Azure e Office 365 para uma organização.

- **Proprietário do Key Vault**: um proprietário do cofre pode criar um cofre de chaves e obter acesso total e controlo sobre o mesmo. O proprietário do cofre também pode configurar uma auditoria ao registo de quem acede a segredos e chaves. Os administradores podem controlar o ciclo de vida das chaves. Podem implementar uma nova versão da chave, fazer uma cópia de segurança e executar tarefas relacionadas.

- **Consumidor do cofre**: um consumidor do cofre pode efetuar ações nos recursos dentro do cofre de chaves quando o proprietário do cofre lhe concede acesso. As ações disponíveis dependem das permissões concedidas.

- **Recurso**: um recurso é um item gerível que está disponível através do Azure. Exemplos comuns são máquina virtual, conta de armazenamento, web app, base de dados e rede virtual. Há muitos mais.

- **Grupo de recursos**: um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.

- **Serviço principal**: Um serviço azure principal é uma identidade de segurança que aplicações, serviços e ferramentas de automação criadas pelo utilizador usam para aceder a recursos específicos do Azure. Pense nisso como uma "identidade de utilizador" (nome de utilizador e senha ou certificado) com uma função específica e permissões rigorosamente controladas. Um principal de serviço apenas precisa de fazer determinadas coisas, ao contrário das identidades de utilizador gerais. Melhora a segurança se lhe conceder apenas o nível mínimo de permissão que necessita para executar as suas tarefas de gestão.

- [Azure Ative Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD é o serviço ative diretório para um inquilino. Cada diretório tem um ou mais domínios. Um diretório pode ter várias subscrições associadas, mas apenas um inquilino.

- **ID do inquilino do Azure**: um ID do inquilino é uma forma exclusiva para identificar uma instância do Azure AD numa subscrição do Azure.

- **Identidades geridas**: O Cofre chave Azure fornece uma forma de armazenar de forma segura credenciais e outras chaves e segredos, mas o seu código precisa de autenticar o Key Vault para as recuperar. A utilização de uma identidade gerida torna mais simples a resolução deste problema, conferindo aos serviços Azure uma identidade gerida automaticamente em Azure AD. Pode utilizar esta identidade para autenticar no Key Vault ou em qualquer serviço que suporta a autenticação Azure AD, sem ser necessário ter credenciais no seu código. Para mais informações, consulte a imagem seguinte e a [visão geral das identidades geridas para os recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

    ![Diagrama de como as identidades geridas para os recursos do Azure funcionam](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autenticação
Para fazer qualquer operação com o Cofre chave, primeiro precisa autenticar. Há três maneiras de autenticar o Cofre chave:

- [Identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md): Quando você implementa uma aplicação numa máquina virtual em Azure, você pode atribuir uma identidade à sua máquina virtual que tem acesso a Key Vault. Também pode atribuir identidades a [outros recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md) O benefício desta abordagem é que a app ou serviço não está a gerir a rotação do primeiro segredo. Azure gira automaticamente a identidade. Recomendamos esta abordagem como uma boa prática. 
- Diretor de **serviço e certificado:** Pode utilizar um diretor de serviço e um certificado associado que tenha acesso ao Key Vault. Não recomendamos esta abordagem porque o proprietário ou desenvolvedor da aplicação deve rodar o certificado.
- **Diretor**de serviço e secreto : Embora possa usar um diretor de serviço e um segredo para autenticar o Cofre chave, não o recomendamos. É difícil rodar automaticamente o segredo da armadilha que é usado para autenticar o Cofre chave.


## <a name="key-vault-roles"></a>Funções do Key Vault

Utilize a tabela seguinte para melhor compreender como a chave de cofre pode ajudar a satisfazer as necessidades dos programadores e dos administradores de segurança.

| Função | Declaração do problema | Resolvido pelo cofre de chave do Azure |
| --- | --- | --- |
| Programador de uma aplicação do Azure |"Quero escrever um pedido para o Azure que usa chaves para assinar e encriptar. Mas quero que estas chaves sejam externas da minha aplicação para que a solução seja adequada para uma aplicação que seja distribuída geograficamente. <br/><br/>Quero que as chaves e os segredos estejam protegidos, sem que tenha de escrever eu próprio o código. E também quero que seja fácil utilizar as chaves e os segredos a partir das minhas aplicações, com um desempenho ótimo”. |As chaves √ são armazenadas num cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware.<br/><br/> √ As chaves são processadas nos HSMs que residem no mesmos datacenters do Azure que as aplicações. Este método proporciona mais fiabilidade e latência reduzida do que as chaves que residem numa localização separada, como no local. |
| Programador de software como um serviço (SaaS) |"Não quero a responsabilidade ou um potencial encargo para chaves e segredos dos inquilino dos meus clientes. <br/><br/>Quero que os clientes sejam proprietários e efetuem a gestão das suas próprias chaves para que eu me possa concentrar no que sei fazer de melhor, ou seja, fornecer as principais funcionalidades de software." |Os clientes √ podem importar as suas próprias chaves para o Azure e geri-las. Quando uma aplicação SaaS precisa de realizar operações criptográficas utilizando as chaves dos clientes, a Key Vault realiza estas operações em nome da aplicação. A aplicação não consegue ver as chaves dos clientes. |
| Responsável pela segurança (CSO) |"Quero saber se as nossas aplicações estão em conformidade com os HSMs FIPS 140-2 de nível 2 para uma gestão de chaves segura. <br/><br/>Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas. <br/><br/>E embora utilizemos vários serviços e recursos do Azure, pretendo gerir as chaves a partir de uma única localização no Azure." |Os HSMs √ têm a certificação FIPS 140-2 de nível 2 validada.<br/><br/>A chave do cofre √ foi concebida para que a Microsoft não consulte ou extraia as suas chaves.<br/><br/>√ A utilização da chave é registada em tempo quase real.<br/><br/>√ O cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure, de quantas regiões suporta e quais as aplicações em que os utiliza. |

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chave. Embora a Key Vault beneficie os desenvolvedores e administradores de segurança, pode ser implementada e gerida por um administrador de uma organização que gere outros serviços azure. Por exemplo, este administrador pode iniciar sessão com uma assinatura Azure, criar um cofre para a organização em que armazenar chaves, e depois ser responsável por tarefas operacionais como estas:

- Criar ou importar uma chave ou segredo
- Revogar ou eliminar uma chave ou segredo
- Autorizar aos utilizadores ou aplicações o acesso à chave de cofre para que possam gerir ou utilizar as suas chaves e segredos
- Configurar a utilização da chave (por exemplo, iniciar sessão ou encriptar)
- Monitorizar a utilização da chave

Este administrador dá então aos desenvolvedores URIs para ligarem a partir das suas aplicações. Este administrador também fornece informações de registo de uso chave ao administrador de segurança. 

![Visão geral de como funciona o Cofre de Chaves Azure][1]

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o [manual do programador da chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passos seguintes

Aprenda a [proteger o seu cofre.](key-vault-secure-your-key-vault.md)

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

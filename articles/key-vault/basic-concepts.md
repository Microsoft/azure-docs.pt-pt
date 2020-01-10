---
title: O que é o Azure Key Vault? | Microsoft Docs
description: Saiba como o Azure Key Vault protege chaves criptográficas e segredos usados por aplicativos e serviços de nuvem.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 7f32664f4bb9407d915b7909f9ecdeebe96bd551
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771943"
---
# <a name="azure-key-vault-basic-concepts"></a>Conceitos básicos do Azure Key Vault

O Azure Key Vault é uma ferramenta para armazenar e aceder a segredos em segurança. Um segredo é tudo cujo acesso deseja controlar rigidamente, como chaves de API, palavras-passe ou certificados. Um cofre é um grupo lógico de segredos.

Aqui estão outros termos importantes:

- **Inquilino**: um inquilino é a organização que possui e gere uma instância específica de serviços cloud da Microsoft. Geralmente, é usado para se referir ao conjunto de serviços do Azure e do Office 365 para uma organização.

- **Proprietário do Key Vault**: um proprietário do cofre pode criar um cofre de chaves e obter acesso total e controlo sobre o mesmo. O proprietário do cofre também pode configurar uma auditoria ao registo de quem acede a segredos e chaves. Os administradores podem controlar o ciclo de vida das chaves. Podem implementar uma nova versão da chave, fazer uma cópia de segurança e executar tarefas relacionadas.

- **Consumidor do cofre**: um consumidor do cofre pode efetuar ações nos recursos dentro do cofre de chaves quando o proprietário do cofre lhe concede acesso. As ações disponíveis dependem das permissões concedidas.

- **Recurso**: um recurso é um item gerível que está disponível através do Azure. Exemplos comuns são máquina virtual, conta de armazenamento, aplicativo Web, banco de dados e rede virtual. Há muito mais.

- **Grupo de recursos**: um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.

- **Entidade de serviço**: uma entidade de serviço do Azure é uma identidade de segurança que os aplicativos, serviços e ferramentas de automação criados pelo usuário usam para acessar recursos específicos do Azure. Imagine-o como uma "identidade do usuário" (nome de usuários e senha ou certificado) com uma função específica e permissões rigidamente controladas. Um principal de serviço apenas precisa de fazer determinadas coisas, ao contrário das identidades de utilizador gerais. Ele melhora a segurança se você conceder apenas o nível mínimo de permissão necessário para executar suas tarefas de gerenciamento.

- [Azure Active Directory (AD do Azure)](../active-directory/active-directory-whatis.md): o Azure AD é o serviço de Active Directory para um locatário. Cada diretório tem um ou mais domínios. Um diretório pode ter várias subscrições associadas, mas apenas um inquilino.

- **ID do inquilino do Azure**: um ID do inquilino é uma forma exclusiva para identificar uma instância do Azure AD numa subscrição do Azure.

- **Identidades gerenciadas**: o Azure Key Vault fornece uma maneira de armazenar credenciais e outras chaves e segredos com segurança, mas seu código precisa autenticar-se no Key Vault para recuperá-los. O uso de uma identidade gerenciada torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Pode utilizar esta identidade para autenticar no Key Vault ou em qualquer serviço que suporta a autenticação Azure AD, sem ser necessário ter credenciais no seu código. Para obter mais informações, consulte a imagem a seguir e a [visão geral de identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagrama de como as identidades gerenciadas dos recursos do Azure funcionam](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autenticação
Para realizar operações com Key Vault, primeiro você precisa se autenticar nele. Há três maneiras de se autenticar no Key Vault:

- [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md): ao implantar um aplicativo em uma máquina virtual no Azure, você pode atribuir uma identidade à sua máquina virtual que tem acesso ao key Vault. Você também pode atribuir identidades a [outros recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). O benefício dessa abordagem é que o aplicativo ou serviço não está gerenciando a rotação do primeiro segredo. O Azure gira automaticamente a identidade. Recomendamos essa abordagem como uma prática recomendada. 
- **Entidade de serviço e certificado**: você pode usar uma entidade de serviço e um certificado associado que tenha acesso a Key Vault. Não recomendamos essa abordagem porque o proprietário do aplicativo ou o desenvolvedor deve girar o certificado.
- **Entidade de serviço e segredo**: embora você possa usar uma entidade de serviço e um segredo para autenticar para Key Vault, não é recomendável. É difícil girar automaticamente o segredo de inicialização que é usado para autenticar para Key Vault.


## <a name="key-vault-roles"></a>Funções do Key Vault

Utilize a tabela seguinte para melhor compreender como a chave de cofre pode ajudar a satisfazer as necessidades dos programadores e dos administradores de segurança.

| Função | Declaração do problema | Resolvido pelo cofre de chave do Azure |
| --- | --- | --- |
| Programador de uma aplicação do Azure |"Desejo escrever um aplicativo para o Azure que usa chaves para assinatura e criptografia. Mas quero que essas chaves sejam externas do meu aplicativo para que a solução seja adequada para um aplicativo distribuído geograficamente. <br/><br/>Quero que as chaves e os segredos estejam protegidos, sem que tenha de escrever eu próprio o código. E também quero que seja fácil utilizar as chaves e os segredos a partir das minhas aplicações, com um desempenho ótimo”. |As chaves √ são armazenadas num cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware.<br/><br/> √ As chaves são processadas nos HSMs que residem no mesmos datacenters do Azure que as aplicações. Este método proporciona mais fiabilidade e latência reduzida do que as chaves que residem numa localização separada, como no local. |
| Programador de software como um serviço (SaaS) |"Não quero a responsabilidade ou um potencial encargo para chaves e segredos dos inquilino dos meus clientes. <br/><br/>Quero que os clientes sejam proprietários e efetuem a gestão das suas próprias chaves para que eu me possa concentrar no que sei fazer de melhor, ou seja, fornecer as principais funcionalidades de software." |Os clientes √ podem importar as suas próprias chaves para o Azure e geri-las. Quando um aplicativo SaaS precisa executar operações criptográficas usando as chaves dos clientes, Key Vault realiza essas operações em nome do aplicativo. A aplicação não consegue ver as chaves dos clientes. |
| Responsável pela segurança (CSO) |"Quero saber se as nossas aplicações estão em conformidade com os HSMs FIPS 140-2 de nível 2 para uma gestão de chaves segura. <br/><br/>Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas. <br/><br/>E embora utilizemos vários serviços e recursos do Azure, pretendo gerir as chaves a partir de uma única localização no Azure." |Os HSMs √ têm a certificação FIPS 140-2 de nível 2 validada.<br/><br/>A chave do cofre √ foi concebida para que a Microsoft não consulte ou extraia as suas chaves.<br/><br/>√ A utilização da chave é registada em tempo quase real.<br/><br/>√ O cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure, de quantas regiões suporta e quais as aplicações em que os utiliza. |

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chave. Embora Key Vault beneficia os desenvolvedores e administradores de segurança, eles podem ser implementados e gerenciados pelo administrador de uma organização que gerencia outros serviços do Azure. Por exemplo, esse administrador pode entrar com uma assinatura do Azure, criar um cofre para a organização na qual armazenar chaves e, em seguida, ser responsável por tarefas operacionais como estas:

- Criar ou importar uma chave ou segredo
- Revogar ou eliminar uma chave ou segredo
- Autorizar aos utilizadores ou aplicações o acesso à chave de cofre para que possam gerir ou utilizar as suas chaves e segredos
- Configurar a utilização da chave (por exemplo, iniciar sessão ou encriptar)
- Monitorizar a utilização da chave

Esse administrador então fornece aos desenvolvedores os URIs para chamar de seus aplicativos. Esse administrador também fornece informações de log de uso de chave para o administrador de segurança. 

![Visão geral de como Azure Key Vault funciona][1]

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o [manual do programador da chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como [proteger seu cofre](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

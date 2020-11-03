---
title: O que é o cofre de chave do Azure? | Microsoft Docs
description: Saiba como o Azure Key Vault protege chaves criptográficas e segredos que as aplicações e serviços em nuvem usam.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c1da45115303bb0a67d6ff796a40ef47c24224a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287426"
---
# <a name="azure-key-vault-basic-concepts"></a>Conceitos básicos Azure Key Vault

Azure Key Vault é um serviço de nuvem para armazenar e aceder a segredos de forma segura. Um segredo é qualquer coisa a que queira controlar firmemente o acesso, como chaves API, palavras-passe, certificados ou chaves criptográficas. O serviço Key Vault suporta dois tipos de contentores: cofres e piscinas HSM geridas. Os cofres suportam o software de armazenamento e chaves, segredos e certificados apoiados pelo HSM. As piscinas HSM geridas suportam apenas as teclas apoiadas por HSM. Consulte [a visão geral da Azure Key Vault REST API](about-keys-secrets-certificates.md) para obter detalhes completos.

Aqui estão outros termos importantes:

- **Inquilino** : um inquilino é a organização que possui e gere uma instância específica de serviços cloud da Microsoft. É mais frequentemente usado para se referir ao conjunto de serviços Azure e Microsoft 365 para uma organização.

- **Proprietário do Key Vault** : um proprietário do cofre pode criar um cofre de chaves e obter acesso total e controlo sobre o mesmo. O proprietário do cofre também pode configurar uma auditoria ao registo de quem acede a segredos e chaves. Os administradores podem controlar o ciclo de vida das chaves. Podem implementar uma nova versão da chave, fazer uma cópia de segurança e executar tarefas relacionadas.

- **Consumidor do cofre** : um consumidor do cofre pode efetuar ações nos recursos dentro do cofre de chaves quando o proprietário do cofre lhe concede acesso. As ações disponíveis dependem das permissões concedidas.

- **Administradores geridos do HSM** : Os utilizadores a quem é atribuída a função de Administrador têm controlo total sobre um pool gerido do HSM. Podem criar mais atribuições de funções para delegar acesso controlado a outros utilizadores.

- **Gerido HSM Crypto Officer/Utilizador** : Funções incorporadas que são normalmente atribuídas aos utilizadores ou principais de serviço que executarão operações criptográficas usando chaves em HSM Gerido. O Utilizador Crypto pode criar novas teclas, mas não consegue eliminar as teclas.

- **Encriptação gerida do Serviço Crypto HSM** : Função incorporada que é normalmente atribuída a uma conta de serviço gerida (por exemplo, conta de armazenamento) para encriptação de dados em repouso com a chave gerida pelo cliente.

- **Recurso** : um recurso é um item gerível que está disponível através do Azure. Exemplos comuns são máquina virtual, conta de armazenamento, aplicação web, base de dados e rede virtual. Há muitos mais.

- **Grupo de recursos** : um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.

- **Principal de segurança** : Um principal de segurança Azure é uma identidade de segurança que aplicações, serviços e ferramentas de automação criados pelo utilizador usam para aceder a recursos específicos do Azure. Pense nisso como uma "identidade de utilizador" (nome de utilizador e palavra-passe ou certificado) com uma função específica e permissões rigorosamente controladas. Um diretor de segurança só deve fazer coisas específicas, ao contrário de uma identidade geral do utilizador. Melhora a segurança se lhe conceder apenas o nível mínimo de autorização de que necessita para executar as suas tarefas de gestão. Um principal de segurança utilizado com uma aplicação ou serviço é especificamente chamado de **principal de serviço.**

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): o Azure AD é o serviço do Active Directory para um inquilino. Cada diretório tem um ou mais domínios. Um diretório pode ter várias subscrições associadas, mas apenas um inquilino.

- **ID do inquilino do Azure** : um ID do inquilino é uma forma exclusiva para identificar uma instância do Azure AD numa subscrição do Azure.

- **Identidades geridas** : O Azure Key Vault fornece uma forma de armazenar credenciais e outros segredos de segurança, mas o seu código precisa de autenticar para o Key Vault para as recuperar. A utilização de uma identidade gerida torna a resolução deste problema mais simples, dando aos serviços Azure uma identidade gerida automaticamente em Azure AD. Pode utilizar esta identidade para autenticar no Key Vault ou em qualquer serviço que suporta a autenticação Azure AD, sem ser necessário ter credenciais no seu código. Para mais informações, consulte a imagem a seguir e a [visão geral das identidades geridas para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

    ![Diagrama de como as identidades geridas para os recursos da Azure funcionam](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autenticação
Para fazer qualquer operação com o Key Vault, primeiro tem de autenticar. Há três maneiras de autenticar o Cofre de Chaves:

- [Identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md): Quando implementa uma aplicação numa máquina virtual em Azure, pode atribuir uma identidade à sua máquina virtual que tenha acesso ao Key Vault. Também pode atribuir identidades a [outros recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md). O benefício desta abordagem é que a app ou serviço não está a gerir a rotação do primeiro segredo. O Azure roda automaticamente a identidade. Recomendamos esta abordagem como uma boa prática. 
- **Principal de serviço e certificado** : Você pode usar um principal serviço e um certificado associado que tem acesso ao Key Vault. Não recomendamos esta abordagem porque o proprietário ou desenvolvedor da aplicação deve rodar o certificado.
- **Diretor de serviço e segredo** : Embora possa usar um diretor de serviço e um segredo para autenticar para Key Vault, não o recomendamos. É difícil rodar automaticamente o segredo da bota que é usado para autenticar para o Key Vault.


## <a name="key-vault-roles"></a>Funções do Key Vault

Utilize a tabela seguinte para melhor compreender como a chave de cofre pode ajudar a satisfazer as necessidades dos programadores e dos administradores de segurança.

| Função | Declaração do problema | Resolvido pelo cofre de chave do Azure |
| --- | --- | --- |
| Programador de uma aplicação do Azure |"Quero escrever uma aplicação para o Azure que usa chaves para assinar e encriptar. Mas quero que estas chaves sejam externas da minha aplicação para que a solução seja adequada para uma aplicação que seja distribuída geograficamente. <br/><br/>Quero que as chaves e os segredos estejam protegidos, sem que tenha de escrever eu próprio o código. Também quero que estas chaves e segredos sejam fáceis de usar a partir das minhas aplicações, com o melhor desempenho." |As chaves √ são armazenadas num cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware.<br/><br/> √ As chaves são processadas nos HSMs que residem no mesmos datacenters do Azure que as aplicações. Este método proporciona mais fiabilidade e latência reduzida do que as chaves que residem numa localização separada, como no local. |
| Programador de software como um serviço (SaaS) |"Não quero a responsabilidade ou potencial responsabilidade pelas chaves e segredos dos meus clientes. <br/><br/>Quero que os clientes possuam e gerem as suas chaves para que me concentre em fazer o que faço melhor, que é fornecer as principais funcionalidades de software." |Os clientes √ podem importar as suas próprias chaves para o Azure e geri-las. Quando uma aplicação SaaS precisa de realizar operações criptográficas utilizando as chaves dos clientes, o Key Vault faz estas operações em nome da aplicação. A aplicação não vê as chaves dos clientes. |
| Responsável pela segurança (CSO) |"Quero saber que as nossas aplicações cumprem os FIPS 140-2 Nível 2 ou FIPS 140-2 Nível 3 HSMs para uma gestão segura das chaves. <br/><br/>Pretendo certificar-me de que a minha organização está a controlar o ciclo de vida das chave e pode monitorizar a utilização das mesmas. <br/><br/>E apesar de usarmos vários serviços e recursos da Azure, quero gerir as chaves de um único local em Azure." |√ Escolha **os cofres** para HSMs validados FIPS 140-2 Nível 2.<br/>√ Escolha **piscinas HSM geridas** para HSMs validados FIPS 140-2 Nível 3.<br/><br/>A chave do cofre √ foi concebida para que a Microsoft não consulte ou extraia as suas chaves.<br/>√ A utilização da chave é registada em tempo quase real.<br/><br/>√ O cofre fornece uma interface única, independentemente de quantos cofres tiver no Azure, de quantas regiões suporta e quais as aplicações em que os utiliza. |

Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres de chave. Embora o Key Vault beneficie os desenvolvedores e os administradores de segurança, pode ser implementado e gerido por um administrador de uma organização que gere outros serviços Azure. Por exemplo, este administrador pode iniciar sessão com uma assinatura Azure, criar um cofre para a organização para armazenar chaves e, em seguida, ser responsável por tarefas operacionais como estas:

- Criar ou importar uma chave ou segredo
- Revogar ou eliminar uma chave ou segredo
- Autorizar aos utilizadores ou aplicações o acesso à chave de cofre para que possam gerir ou utilizar as suas chaves e segredos
- Configurar a utilização da chave (por exemplo, iniciar sessão ou encriptar)
- Monitorizar a utilização da chave

Este administrador dá então uris aos desenvolvedores para ligar a partir das suas aplicações. Este administrador também fornece informações de registo de utilização chave ao administrador de segurança. 

![Visão geral de como funciona o Azure Key Vault][1]

Os programadores também podem gerir as chaves diretamente, com APIs. Para obter mais informações, consulte o [manual do programador da chave](developers-guide.md).

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [proteger o cofre.](secure-your-key-vault.md)
- Saiba como [garantir as suas piscinas HSM geridas](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).
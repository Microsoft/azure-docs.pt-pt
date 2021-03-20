---
title: Segurança do Serviço Azure Blockchain
description: Azure Blockchain Service acesso a dados e conceitos de segurança
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80879603"
---
# <a name="azure-blockchain-service-security"></a>Segurança do Serviço Azure Blockchain

O Azure Blockchain Service utiliza várias capacidades Azure para manter os seus dados seguros e disponíveis. Os dados são protegidos usando isolamento, encriptação e autenticação.

## <a name="isolation"></a>Isolamento

Os recursos do Serviço Azure Blockchain estão isolados numa rede virtual privada. Cada nó de transação e validação é uma máquina virtual (VM). Os VMs numa rede virtual não podem comunicar diretamente com VMs numa rede virtual diferente. O isolamento garante que a comunicação permanece privada dentro da rede virtual. Para obter mais informações sobre o isolamento da rede virtual Azure, consulte [o isolamento na Nuvem Pública de Azure.](../../security/fundamentals/isolation-choices.md#networking-isolation)

![Diagrama VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Encriptação

Os dados do utilizador são armazenados no armazenamento do Azure. Os dados do utilizador são encriptados em movimento e em repouso para segurança e confidencialidade. Para mais informações, consulte: [Guia de segurança do Azure Storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Autenticação

As transações podem ser enviadas para os nós blockchain através de um ponto final RPC. Os clientes comunicam com um nó de transação utilizando um servidor de procuração inversa que trata da autenticação do utilizador e encripta dados através do TLS.

![Diagrama de autenticação](./media/data-security/authentication.png)

Existem três modos de autenticação para acesso RPC.

### <a name="basic-authentication"></a>Autenticação básica

A autenticação básica utiliza um cabeçalho de autenticação HTTP contendo o nome de utilizador e a palavra-passe. Nome de utilizador é o nome do nó blockchain. A palavra-passe é definida durante o fornecimento de um membro ou nó. A palavra-passe pode ser alterada utilizando o portal Azure ou CLI.

### <a name="access-keys"></a>Chaves de acesso

As teclas de acesso utilizam uma cadeia gerada aleatoriamente incluída no URL do ponto final. Duas teclas de acesso ajudam a ativar a rotação da chave. As chaves podem ser regeneradas a partir do portal Azure e do CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Ative Directory (Azure AD) utiliza um mecanismo de autenticação baseado em sinistros onde o utilizador é autenticado pela Azure AD utilizando credenciais de utilizador da Azure AD. O Azure AD fornece uma gestão de identidade baseada na nuvem e permite que os clientes utilizem uma única identidade em toda uma empresa e acedam a aplicações na nuvem. O Azure Blockchain Service integra-se com a Azure AD permitindo a federação de ID, a autenticação única e multi-factor. Pode atribuir aos utilizadores, grupos e funções de aplicação na sua organização para o acesso de membros blockchain e nó.

O proxy do cliente Azure AD está disponível no [GitHub.](https://github.com/Microsoft/azure-blockchain-connector/releases) O proxy do cliente direciona o utilizador para a página de login Azure AD e obtém um sinal ao portador após a autenticação bem sucedida. Posteriormente, o utilizador liga uma aplicação do cliente Ethereum, como Geth ou Truffle, ao ponto final do representante do cliente. Finalmente, quando uma transação é submetida, o representante do cliente injeta o token do portador no cabeçalho http e o representante inverso valida o token usando o protocolo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Contas Keys e Ethereum

Ao forer um membro do Serviço Azure Blockchain, é gerada uma conta Ethereum e um par de chaves público e privado. A chave privada é usada para enviar transações para a blockchain. A conta Ethereum é o último 20 bytes do haxixe da chave pública. A conta Ethereum também é chamada de carteira.

O par de chaves privada e pública é armazenado como um ficheiro chave no formato JSON. A chave privada é encriptada usando a palavra-passe inserida quando o serviço de contabilidade blockchain é criado.

As chaves privadas são usadas para assinar transações digitalmente. Em blockchains privados, um contrato inteligente assinado por uma chave privada representa a identidade do signatário. Para verificar a validade da assinatura, o recetor pode comparar a chave pública do signatário com o endereço calculado a partir da assinatura.

As teclas de constelação são usadas para identificar um nó quórum. As teclas de constelação são geradas no momento do fornecimento de nó e são especificadas no parâmetro privado de uma transação privada em Qurum.

## <a name="next-steps"></a>Passos seguintes

Ver [Como configurar o acesso ao Azure Ative Directory para o Serviço Azure Blockchain](configure-aad.md).

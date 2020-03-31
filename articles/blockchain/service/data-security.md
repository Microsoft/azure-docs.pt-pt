---
title: Segurança do Serviço Azure Blockchain
description: Acesso de dados e conceitos de segurança do Serviço Blockchain Azure
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982232"
---
# <a name="azure-blockchain-service-security"></a>Segurança do Serviço Azure Blockchain

O Serviço Azure Blockchain utiliza várias capacidades Azure para manter os seus dados seguros e disponíveis. Os dados são protegidos através do isolamento, encriptação e autenticação.

## <a name="isolation"></a>Isolamento

Os recursos do Serviço Azure Blockchain estão isolados numa rede virtual privada. Cada nó de transação e validação é uma máquina virtual (VM). VMs em uma rede virtual não podem comunicar diretamente com VMs em uma rede virtual diferente. O isolamento garante que a comunicação permanece privada dentro da rede virtual. Para obter mais informações sobre o isolamento da rede virtual Azure, consulte [o isolamento na Nuvem Pública azure.](../../security/fundamentals/isolation-choices.md#networking-isolation)

![Diagrama vNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Encriptação

Os dados dos utilizadores são armazenados no armazenamento do Azure. Os dados dos utilizadores são encriptados em movimento e em repouso para segurança e confidencialidade. Para mais informações, consulte: Guia de [segurança azure Storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Autenticação

As transações podem ser enviadas para os nódosos blockchain através de um ponto final rPC. Os clientes comunicam com um nó de transação utilizando um servidor proxy invertido que lida com a autenticação do utilizador e encripta dados através do SSL.

![Diagrama de autenticação](./media/data-security/authentication.png)

Existem três modos de autenticação para acesso RPC.

### <a name="basic-authentication"></a>Autenticação básica

A autenticação básica utiliza um cabeçalho de autenticação HTTP contendo o nome de utilizador e a palavra-passe. O nome do utilizador é o nome do nó blockchain. A palavra-passe é definida durante o fornecimento de um membro ou nó. A palavra-passe pode ser alterada utilizando o portal Azure ou o CLI.

### <a name="access-keys"></a>Chaves de acesso

As teclas de acesso utilizam uma cadeia gerada aleatoriamente incluída no URL final. Duas teclas de acesso ajudam a ativar a rotação da chave. As chaves podem ser regeneradas a partir do portal Azure e clI.

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Ative Directory (Azure AD) utiliza um mecanismo de autenticação baseado em reclamações onde o utilizador é autenticado pela Azure AD utilizando credenciais de utilizador da Azure AD. A Azure AD fornece gestão de identidade baseada na nuvem e permite que os clientes utilizem uma única identidade em toda uma empresa e aplicações de acesso na nuvem. O Azure Blockchain Service integra-se com a Azure AD permitindo a federação de ID, a autenticação de entrada única e multi-factor. Pode atribuir aos utilizadores, grupos e aplicações na sua organização para o acesso ao membro blockchain e ao nó.

O representante do cliente Da Azure AD está disponível no [GitHub.](https://github.com/Microsoft/azure-blockchain-connector/releases) O representante do cliente direciona o utilizador para a página de login da AD Azure e obtém um token ao portador após autenticação bem sucedida. Posteriormente, o utilizador liga uma aplicação de cliente Ethereum, como Geth ou Truffle, ao ponto final do cliente proxy. Finalmente, quando uma transação é submetida, o representante do cliente injeta o token do portador no cabeçalho http e o representante inverso valida o token usando o protocolo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Contas Keys e Ethereum

Ao fornecer um membro do Serviço Azure Blockchain, é gerada uma conta Ethereum e um par de chaves público salutar. A chave privada é usada para enviar transações para a blockchain. A conta Ethereum é os últimos 20 bytes do hash da chave pública. A conta Ethereum também é chamada de carteira.

O par de chaves privados e públicos é armazenado como um porta-chaves no formato JSON. A chave privada é encriptada utilizando a palavra-passe introduzida quando o serviço de contabilidade blockchain é criado.

As chaves privadas são usadas para assinar transações digitalmente. Em blockchains privados, um contrato inteligente assinado por uma chave privada representa a identidade do signatário. Para verificar a validade da assinatura, o recetor pode comparar a chave pública do signatário com o endereço calculado a partir da assinatura.

As chaves da constelação são usadas para identificar um nó quórum. As chaves de constelação são geradas no momento do fornecimento do nó e são especificadas no parâmetro privado Para uma transação privada no Quórum.

## <a name="next-steps"></a>Passos seguintes

Ver [Como configurar o acesso ao Diretório Ativo Azure para o Serviço Blockchain Azure](configure-aad.md).

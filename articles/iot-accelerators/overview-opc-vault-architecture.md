---
title: Arquitetura do cofre OPC – Azure | Microsoft Docs
description: Arquitetura do serviço de gerenciamento de certificados do cofre OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200157"
---
# <a name="opc-vault-architecture"></a>Arquitetura do cofre OPC

Este artigo fornece uma visão geral sobre o microserviço do cofre OPC e o módulo de IoT Edge do cofre OPC.

Os aplicativos OPC UA usam certificados de instância de aplicativo para fornecer segurança em nível de aplicativo. Uma conexão segura é estabelecida usando a criptografia assimétrica, para a qual os certificados de aplicativo fornecem o par de chaves pública e privada. Os certificados podem ser autoassinados ou assinados por uma autoridade de certificação (CA).

Um aplicativo OPC UA tem uma lista de certificados confiáveis que representa os aplicativos aos quais ele confia. Esses certificados podem ser autoassinados ou assinados por uma CA, ou podem ser uma CA raiz ou uma subautoridade de certificação em si. Se um certificado confiável fizer parte de uma cadeia de certificados maior, o aplicativo confiará em todos os certificados que se encadeadom ao certificado na lista de confiança. Isso é verdadeiro, desde que a cadeia de certificados completa possa ser validada.

A principal diferença entre a confiança de certificados autoassinados e a confiança de um certificado de autoridade de certificação é o esforço de instalação necessário para implantar e manter a confiança. Também há um esforço adicional para hospedar uma autoridade de certificação específica da empresa. 

Para distribuir a confiança para certificados autoassinados para vários servidores com um único aplicativo cliente, você deve instalar todos os certificados de aplicativo do servidor na lista de confiança do aplicativo cliente. Além disso, você deve instalar o certificado do aplicativo cliente em todas as listas de confiança do aplicativo do servidor. Esse esforço administrativo é um fardo e até mesmo aumenta quando você precisa considerar os tempos de vida do certificado e renovar os certificados.

O uso de uma autoridade de certificação específica da empresa pode simplificar muito o gerenciamento da confiança com vários servidores e clientes. Nesse caso, o administrador gera um certificado de instância de aplicativo assinado por AC uma vez para cada cliente e servidor usado. Além disso, o certificado de autoridade de certificação é instalado em todas as listas de confiança de aplicativos, em todos os servidores e clientes. Com essa abordagem, somente os certificados expirados precisam ser renovados e substituídos para os aplicativos afetados.

O serviço de gerenciamento de certificados do IoT UA do Azure industrial do OPC ajuda a gerenciar uma AC específica da empresa para aplicativos OPC UA. Esse serviço é baseado no microserviço do cofre OPC. O cofre OPC fornece um microserviço para hospedar uma AC específica da empresa em uma nuvem segura. Essa solução é apoiada por serviços protegidos pelo Azure Active Directory (Azure AD), Azure Key Vault com HSMs (módulos de segurança de hardware), Azure Cosmos DB e, opcionalmente, o Hub IoT como um repositório de aplicativos.

O microserviço do cofre OPC foi projetado para oferecer suporte ao fluxo de trabalho baseado em função, em que administradores de segurança e aprovadores com direitos de assinatura no Azure Key Vault aprovar ou rejeitar solicitações.

Para compatibilidade com as soluções de OPC UA existentes, os serviços incluem suporte para um módulo de borda com backup em microserviço do compartimento OPC. Isso implementa o **servidor de descoberta global do OPC UA e** a interface de gerenciamento de certificados, para distribuir certificados e listas de confiança de acordo com a parte 12 da especificação. 


## <a name="architecture"></a>Arquitetura

A arquitetura é baseada no microserviço do cofre OPC, com um cofre do OPC IoT Edge módulo para a rede de fábrica e um exemplo de UX da Web para controlar o fluxo de trabalho:

![Diagrama da arquitetura do cofre do OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microserviço do cofre OPC

O microserviço do cofre OPC consiste nas seguintes interfaces para implementar o fluxo de trabalho para distribuir e gerenciar uma AC específica da empresa para aplicativos OPC UA.

### <a name="application"></a>Aplicação 
- Um aplicativo OPC UA pode ser um servidor ou um cliente, ou ambos. O cofre OPC serve, nesse caso, como uma autoridade de registro de aplicativo. 
- Além das operações básicas para registrar, atualizar e cancelar o registro de aplicativos, também há interfaces para localizar e consultar aplicativos com expressões de pesquisa. 
- As solicitações de certificado devem fazer referência a um aplicativo válido, a fim de processar uma solicitação e emitir um certificado assinado com todas as extensões específicas do OPC UA. 
- O serviço de aplicativo é apoiado por um banco de dados no Azure Cosmos DB.

### <a name="certificate-group"></a>Grupo de certificados
- Um grupo de certificados é uma entidade que armazena uma autoridade de certificação raiz ou um certificado sub-AC, incluindo a chave privada para assinar certificados. 
- O comprimento de chave RSA, o comprimento do hash SHA-2 e os tempos de vida são configuráveis para a AC do emissor e certificados de aplicativo assinados. 
- Você armazena os certificados de autoridade de certificação em Azure Key Vault, apoiado com o FIPS 140-2 nível 2 HSM. A chave privada nunca deixa o armazenamento seguro, pois a assinatura é feita por uma operação Key Vault protegida pelo Azure AD. 
- Você pode renovar os certificados de autoridade de certificação ao longo do tempo e fazer com que eles permaneçam no armazenamento seguro devido ao histórico de Key Vault. 
- A lista de revogação de cada certificado de autoridade de certificação também é armazenada em Key Vault como um segredo. Quando um aplicativo tem o registro cancelado, o certificado do aplicativo também é revogado na CRL (lista de certificados revogados) por um administrador.
- Você pode revogar certificados únicos, bem como certificados em lote.

### <a name="certificate-request"></a>Solicitação de certificado
Uma solicitação de certificado implementa o fluxo de trabalho para gerar um novo par de chaves ou um certificado assinado, usando uma CSR (solicitação de assinatura de certificado) para um aplicativo OPC UA. 
- A solicitação é armazenada em um banco de dados com informações fornecidas, como o assunto ou um CSR, e uma referência ao aplicativo OPC UA. 
- A lógica de negócios no serviço valida a solicitação em relação às informações armazenadas no banco de dados do aplicativo. Por exemplo, o URI do aplicativo no banco de dados deve corresponder ao URI do aplicativo no CSR.
- Um administrador de segurança com direitos de assinatura (ou seja, a função de aprovador) aprova ou rejeita a solicitação. Se a solicitação for aprovada, um novo par de chaves ou certificado assinado (ou ambos) será gerado. A nova chave privada é armazenada com segurança em Key Vault e o novo certificado público assinado é armazenado no banco de dados de solicitação de certificado.
- O solicitante pode sondar o status da solicitação até que seja aprovado ou revogado. Se a solicitação foi aprovada, a chave privada e o certificado podem ser baixados e instalados no repositório de certificados do aplicativo OPC UA.
- O solicitante agora pode aceitar a solicitação para excluir informações desnecessárias do banco de dados de solicitação. 

Durante o tempo de vida de um certificado assinado, um aplicativo pode ser excluído ou uma chave pode ficar comprometida. Nesse caso, um Gerenciador de CA pode:
- Exclua um aplicativo, que também exclui todas as solicitações de certificado pendentes e aprovadas do aplicativo. 
- Exclua apenas uma solicitação de certificado único, se apenas uma chave for renovada ou comprometida.

Agora, as solicitações de certificado aprovadas e aceitas são marcadas como excluídas.

Um gerente pode renovar regularmente a CRL da autoridade de certificação do emissor. No momento da renovação, todas as solicitações de certificado excluídas são revogadas e os números de série do certificado são adicionados à lista de revogação de CRL. As solicitações de certificado revogadas são marcadas como revogadas. Em eventos urgentes, as solicitações de certificado único também podem ser revogadas.

Por fim, as CRLs atualizadas estão disponíveis para distribuição para os clientes e servidores do OPC UA participantes.

## <a name="opc-vault-iot-edge-module"></a>Módulo de IoT Edge do cofre OPC
Para dar suporte a um servidor de descoberta global de rede de fábrica, você pode implantar o módulo do cofre OPC na borda. Execute-o como um aplicativo local do .NET Core ou inicie-o em um contêiner do Docker. Observe que, devido à falta de suporte à autenticação Auth2 na pilha de .NET Standard do OPC UA atual, a funcionalidade do módulo de borda do OPC Vault é limitada a uma função de leitor. Um usuário não pode ser representado do módulo de borda para o microserviço usando a interface padrão OPC UA GDS.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre a arquitetura do cofre do OPC, você pode:

> [!div class="nextstepaction"]
> [Compilar e implantar o cofre OPC](howto-opc-vault-deploy.md)

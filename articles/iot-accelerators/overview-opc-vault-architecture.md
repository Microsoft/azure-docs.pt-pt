---
title: Arquitetura OPC Vault - Azure Microsoft Docs
description: Arquitetura de serviço de gestão de certificados OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "71200157"
---
# <a name="opc-vault-architecture"></a>Arquitetura do Cofre OPC

Este artigo dá uma visão geral sobre o microserviço opc vault e o módulo OPC Vault IoT Edge.

Os pedidos da OPC UA utilizam certificados de instância de aplicação para garantir a segurança do nível de pedido. Uma ligação segura é estabelecida utilizando a criptografia assimétrica, para a qual os certificados de candidatura fornecem o par de chaves público e privado. Os certificados podem ser auto-assinados ou assinados por uma Autoridade de Certificados (AC).

Um pedido da OPC UA tem uma lista de certificados fidedignos que representam as aplicações em que confia. Estes certificados podem ser auto-assinados ou assinados por um CA, ou podem ser um Root-CA ou um Sub-CA em si. Se um certificado fidedigno fizer parte de uma cadeia de certificados maior, o pedido confia em todos os certificados que acorrentam ao certificado na lista fidedigna. Isto é verdade desde que a cadeia de certificados completo possa ser validada.

A grande diferença entre confiar nos certificados auto-assinados e confiar num certificado de AC é o esforço de instalação necessário para implantar e manter a confiança. Há também um esforço adicional para acolher uma AC específica da empresa. 

Para distribuir a confiança para certificados auto-assinados para vários servidores com uma única aplicação de cliente, deve instalar todos os certificados de aplicação do servidor na lista de confiança da aplicação do cliente. Além disso, deve instalar o certificado de aplicação do cliente em todas as listas de confiança da aplicação do servidor. Este esforço administrativo é um grande fardo, e até aumenta quando se tem de considerar a vida útil dos certificados e renovar os certificados.

O uso de uma AC específica da empresa pode simplificar consideravelmente a gestão da confiança com vários servidores e clientes. Neste caso, o administrador gera um certificado de instância de aplicação assinado pela AC uma vez para cada cliente e servidor utilizado. Além disso, o Certificado CA está instalado em todas as listas de fidedignidade de aplicações, em todos os servidores e clientes. Com esta abordagem, apenas os certificados caducados devem ser renovados e substituídos para as aplicações afetadas.

O serviço de gestão de certificados Azure Industrial IoT OPC UA ajuda-o a gerir um CA específico da empresa para aplicações da OPC UA. Este serviço baseia-se no microserviço OPC Vault. O OPC Vault fornece um microserviço para alojar um CA específico da empresa numa nuvem segura. Esta solução é apoiada por serviços assegurados pela Azure Ative Directory (Azure AD), Azure Key Vault com Módulos de Segurança de Hardware (HSMs), Azure Cosmos DB e opcionalmente IoT Hub como loja de aplicações.

O microserviço OPC Vault foi concebido para apoiar o fluxo de trabalho baseado em papéis, onde administradores de segurança e aprovadores com direitos de assinatura no Cofre chave azure aprovam ou rejeitam pedidos.

Para compatibilidade com as soluções existentes da OPC UA, os serviços incluem suporte para um módulo de borda apoiada por microserviço OPC Vault. Isto implementa a interface **OPC UA Global Discovery Server e Certificate Management,** para distribuir certificados e listas fiduciários de acordo com a Parte 12 da especificação. 


## <a name="architecture"></a>Arquitetura

A arquitetura baseia-se no microserviço OPC Vault, com um módulo OPC Vault IoT Edge para a rede de fábrica e uma amostra web UX para controlar o fluxo de trabalho:

![Diagrama da arquitetura do Cofre opc](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microserviço do Cofre OPC

O microserviço OPC Vault consiste nas seguintes interfaces para implementar o fluxo de trabalho para distribuir e gerir um CA específico da empresa para aplicações OPC UA.

### <a name="application"></a>Aplicação 
- Uma aplicação OPC UA pode ser um servidor ou um cliente, ou ambos. O PcPc Vault serve neste caso como uma autoridade de registo de candidaturas. 
- Para além das operações básicas de registo, atualização e desregisto de aplicações, existem também interfaces para encontrar e consultar aplicações com expressões de pesquisa. 
- Os pedidos de certificado devem fazer referência a um pedido válido, a fim de processar um pedido e emitir um certificado assinado com todas as extensões específicas da OPC UA. 
- O serviço de aplicação é apoiado por uma base de dados em Azure Cosmos DB.

### <a name="certificate-group"></a>Grupo de certificados
- Um grupo de certificados é uma entidade que armazena um certificado ca raiz ou sub CA, incluindo a chave privada para assinar certificados. 
- O comprimento da chave RSA, o comprimento do hash SHA-2, e as vidas são configuráveis tanto para o Emitente CA como para os certificados de candidatura assinados. 
- Guarde os certificados ca no Cofre de Chaves Azure, apoiado seleção fips 140-2 Nível 2 HSM. A chave privada nunca sai do armazenamento seguro, porque a assinatura é feita por uma operação key vault protegida pela Azure AD. 
- Você pode renovar os certificados de AC ao longo do tempo, e fazê-los permanecer em armazenamento seguro devido ao histórico do Cofre chave. 
- A lista de revogação de cada certificado ca também é armazenada no Cofre chave como um segredo. Quando um pedido não está registado, o certificado de pedido também é revogado na Lista de Revogação de Certificados (CRL) por um administrador.
- Pode revogar certificados únicos, bem como certificados emlotas.

### <a name="certificate-request"></a>Pedido de certificado
Um pedido de certificado implementa o fluxo de trabalho para gerar um novo par de chaves ou um certificado assinado, utilizando um Pedido de Assinatura de Certificado (CSR) para um pedido de UA opc. 
- O pedido é armazenado numa base de dados com informações de acompanhamento, como o sujeito ou uma RSE, e uma referência à aplicação da OPC UA. 
- A lógica empresarial do serviço valida o pedido contra a informação armazenada na base de dados de aplicações. Por exemplo, a aplicação Uri na base de dados deve corresponder à aplicação Uri na RSE.
- Um administrador de segurança com direitos de assinatura (isto é, a função de Aprovador) aprova ou rejeita o pedido. Se o pedido for aprovado, é gerado um novo par de chaves ou certificado assinado (ou ambos). A nova chave privada está guardada de forma segura no Key Vault, e o novo certificado público assinado está guardado na base de dados de pedidos de certificados.
- O solicitor pode fazer uma sondagem ao estado do pedido até ser aprovado ou revogado. Se o pedido for aprovado, a chave privada e o certificado podem ser descarregados e instalados na loja de certificados da aplicação OPC UA.
- O solicitador pode agora aceitar o pedido de eliminação de informações desnecessárias da base de dados de pedidos. 

Ao longo da vida útil de um certificado assinado, um pedido pode ser eliminado ou uma chave pode ficar comprometida. Neste caso, um gestor de AC pode:
- Apagar uma aplicação, que também elimina todos os pedidos de certificado pendentes e aprovados da app. 
- Apague apenas um pedido de certificado, se apenas uma chave for renovada ou comprometida.

Os pedidos de certificado aprovados e aceites comprometidos estão marcados como eliminados.

Um gestor pode regularmente renovar o EMitente CA CRL. No momento da renovação, todos os pedidos de certificado eliminados são revogados e os números de série do certificado são adicionados à lista de revogação do CRL. Os pedidos de certificado revogados são marcados como revogados. Em eventos urgentes, os pedidos de certificado único também podem ser revogados.

Finalmente, os CRLs atualizados estão disponíveis para distribuição para os clientes e servidores opc ua participantes.

## <a name="opc-vault-iot-edge-module"></a>Módulo OPC Vault IoT Edge
Para suportar uma rede de fábrica Global Discovery Server, pode implantar o módulo OPC Vault na borda. Execute-o como uma aplicação local .NET Core, ou inicie-o num recipiente Docker. Note que devido à falta de suporte de autenticação Auth2 na atual stack OPC UA .NET Standard, a funcionalidade do módulo de borda do Cofre OPC está limitada a uma função de Leitor. Um utilizador não pode ser personificado do módulo de borda para o microserviço utilizando a interface padrão OPC UA GDS.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a arquitetura do Cofre opc, pode:

> [!div class="nextstepaction"]
> [Construir e implementar o Cofre OPC](howto-opc-vault-deploy.md)

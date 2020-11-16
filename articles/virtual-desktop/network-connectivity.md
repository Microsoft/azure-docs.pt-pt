---
title: Compreender a conectividade da rede virtual do Windows Desktop
titleSuffix: Azure
description: Saiba mais sobre a conectividade da rede virtual de desktop do Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 4c0017a36d84973a4d99c49a5ea33faeb189b35f
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639339"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Compreender a conectividade da rede virtual do Windows Desktop

O Windows Virtual Desktop oferece a capacidade de acolher sessões de clientes nos anfitriões da sessão em execução no Azure. A Microsoft gere partes dos serviços em nome do cliente e fornece pontos finais seguros para conectar clientes e anfitriões de sessão. O diagrama abaixo dá uma visão geral de alto nível das ligações de rede utilizadas pelo Windows Virtual Desktop

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagrama de Conexões de Rede Virtual de Desktop do Windows" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Conectividade da sessão

O Windows Virtual Desktop utiliza o Remote Desktop Protocol (RDP) para fornecer capacidades de visualização e entrada remotas sobre ligações de rede. O RDP foi inicialmente lançado com o Windows NT 4.0 Terminal Server Edition e evoluiu continuamente com todas as versões do Microsoft Windows e Windows Server. Desde o início, a RDP desenvolveu-se para ser independente da sua pilha de transporte subjacente, e hoje suporta vários tipos de transporte.

## <a name="reverse-connect-transport"></a>Transporte de ligação inversa

O Windows Virtual Desktop está a utilizar o transporte de ligação inversa para estabelecer a sessão remota e para transportar tráfego RDP. Ao contrário das implementações de Serviços de Ambiente de Trabalho Remoto no local, o transporte de ligação inversa não utiliza um ouvinte TCP para receber ligações RDP recebidas. Em vez disso, está a utilizar a conectividade de saída para a infraestrutura virtual do Windows desktop sobre a ligação HTTPS.

## <a name="session-host-communication-channel"></a>Canal de comunicação do anfitrião da sessão

Após o arranque do anfitrião da sessão virtual do Windows Desktop, o serviço Remote Desktop Agent Loader estabelece o canal de comunicação persistente do corretor virtual windows. Este canal de comunicação está em camadas em cima de uma ligação segura de Segurança da Camada de Transporte (TLS) e serve como um autocarro para troca de mensagens de serviço entre o anfitrião da sessão e a infraestrutura virtual do Windows Desktop.

## <a name="client-connection-sequence"></a>Sequência de ligação ao cliente

Sequência de ligação do cliente descrita abaixo:

1. Utilizando o utilizador de cliente virtual do Windows Virtual desktop subscreve o Windows Virtual Desktop Workspace
2. O Azure Ative Directory autentica o utilizador e devolve o símbolo utilizado para enumerar os recursos disponíveis para um utilizador
3. Cliente passa ficha para o serviço de subscrição de feed do Windows Virtual Desktop
4. Serviço de subscrição de feed virtual do Windows Desktop valida o token
5. O serviço de subscrição de feed virtual do Windows Desktop passa a lista de ambientes de trabalho disponíveis e RemoteApps de volta ao cliente sob a forma de configuração de ligação assinada digitalmente
6. O cliente armazena a configuração de ligação para cada recurso disponível num conjunto de ficheiros .rdp
7. Quando um utilizador seleciona o recurso para ligar, o cliente utiliza o ficheiro .rdp associado e estabelece a ligação segura TLS 1.2 à instância de gateway virtual do Windows mais próxima e transmite as informações de ligação
8. O Windows Virtual Desktop gateway valida o pedido e pede ao corretor virtual do Windows para orquestrar a ligação
9. O corretor virtual do Windows Desktop identifica o anfitrião da sessão e utiliza o canal de comunicação persistente previamente estabelecido para inicializar a ligação
10. A pilha de ambiente de trabalho remoto inicia a ligação TLS 1.2 à mesma instância de gateway virtual do Windows como utilizada pelo cliente
11. Depois de tanto o cliente como o anfitrião da sessão ligados ao gateway, o gateway começa a transmitir os dados brutos entre ambos os pontos finais, isto estabelece o transporte de ligação inversa base para o PDR
12. Depois de o transporte base estar definido, o cliente inicia o aperto de mão RDP

## <a name="connection-security"></a>Segurança da Ligação

O TLS 1.2 é utilizado para todas as ligações iniciadas a partir dos clientes e anfitriões de sessão para os componentes de infraestrutura virtual do Windows Desktop.
Para o transporte de ligação inversa, tanto o cliente como o anfitrião de sessão ligam-se ao gateway virtual do Windows. Depois de estabelecer a ligação TCP, o cliente ou anfitrião de sessão valida o certificado do Windows Virtual Desktop Gateway.
Após a criação do transporte de base, o RDP estabelece uma ligação TLS aninhada entre o cliente e o anfitrião da sessão utilizando os certificados do anfitrião da sessão. Por predefinição, o certificado utilizado para a encriptação RDP é autogerido pelo SO durante a implementação. Se desejar, os clientes podem utilizar certificados geridos centralmente emitidos pela autoridade de certificação da empresa. Para obter mais informações sobre a configuração dos certificados, consulte a [documentação do Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os requisitos de largura de banda para o Windows Virtual Desktop, consulte [os requisitos de largura de banda do Protocolo de Ambiente de Trabalho Remoto (RDP) para o Windows Virtual Desktop](rdp-bandwidth.md).
* Para começar com a Qualidade de Serviço (QoS) para o Windows Virtual Desktop, consulte [implementar qualidade de serviço (QoS) para Windows Virtual Desktop](rdp-quality-of-service-qos.md).

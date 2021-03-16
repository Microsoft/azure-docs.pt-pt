---
title: Requisitos de infraestrutura sip Interface - Serviços de Comunicação Azure
description: Familiarize-se com os requisitos de infraestrutura para a configuração da interface SIP dos Serviços de Comunicação Azure
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492733"
---
# <a name="sip-interface-infrastructure-requirements"></a>Requisitos de infraestrutura sip Interface 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Este artigo descreve detalhes de conectividade de infraestrutura, licenciamento e controlador de fronteira de sessão (SBC) que você vai querer ter em mente como o seu plano a sua implementação SIP Interface.


## <a name="infrastructure-requirements"></a>Requisitos de infraestrutura
Os requisitos de infraestrutura para os SBCs suportados, domínios e outros requisitos de conectividade de rede para implantar a Interface SIP estão listados no quadro seguinte:  

|Requisito de infraestrutura|Precisa do seguinte.|
|:--- |:--- |
|Controlador de fronteira da sessão (SBC)|Um SBC apoiado. Para obter mais informações, consulte [SBCs suportados.](#supported-session-border-controllers-sbcs)|
|Troncos de telefonia ligados ao SBC|Um ou mais troncos de telefonia ligados ao SBC. Numa das extremidades, o SBC liga-se ao Serviço de Comunicação Azure via Sip Interface. O SBC também pode ligar a entidades de telefonia de terceiros, tais como PBXs, Adaptadores de Telefonia Analógica, e assim por diante. Qualquer opção de conectividade PSTN ligada ao SBC funcionará. (Para configurar os troncos PSTN para o SBC, consulte os fornecedores de SBC ou fornecedores de troncos.)|
|Subscrição do Azure|Uma subscrição Azure que utiliza para criar recurso ACS, e a configuração e ligação ao SBC.|
|Acesso de serviços de comunicação Token|Para estoque de chamadas, precisa de um Access Token válido com `voip` âmbito. Ver [Fichas de Acesso](../identity-model.md#access-tokens)|
|Endereço IP público para o SBC|Um endereço IP público que pode ser usado para ligar ao SBC. Com base no tipo de SBC, o SBC pode usar NAT.|
|Nome de domínio totalmente qualificado (FQDN) para o SBC|Um FQDN para o SBC, onde a parte de domínio do FQDN não corresponde aos domínios registados na sua organização Microsoft 365 ou Office 365. Para obter mais informações, consulte [os nomes do domínio SBC](#sbc-domain-names).|
|Entrada pública de DNS para o SBC |Uma entrada pública de DNS mapeando o SBC FQDN para o endereço IP público. |
|Certificado de confiança pública para o SBC |Um certificado para o SBC ser utilizado para toda a comunicação com a Sip Interface. Para mais informações, consulte [o certificado de confiança pública para o SBC](#public-trusted-certificate-for-the-sbc).|
|Endereços IP de firewall e portas para sinalização SIP e meios de comunicação |O SBC comunica aos seguintes serviços na nuvem:<br/><br/>SIP Proxy, que lida com a sinalização<br/>Processador de mídia, que lida com os meios de comunicação<br/><br/>Estes dois serviços têm endereços IP separados no Microsoft Cloud, descritos mais tarde neste documento.


## <a name="sbc-domain-names"></a>Nomes de domínio SBC

Os clientes sem Office 365 podem utilizar qualquer nome de domínio para o qual possam obter um certificado público.

A tabela a seguir mostra exemplos de nomes DNS registados para o inquilino, se o nome pode ser usado como um nome de domínio totalmente qualificado (FQDN) para o SBC, e exemplos de nomes válidos de FQDN:

|Nome DNS|Pode ser usado para SBC FQDN|Exemplos de nomes FQDN|
|:--- |:--- |:--- |
contoso.com|Sim|**Nomes válidos:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|No|A utilização de domínios *.onmicrosoft.com não é suportada para nomes SBC

Se você é um cliente do Office 365, então o nome de domínio SBC não deve corresponder registado em Domínios do Inquilino Office 365. Segue-se o exemplo da coexistência do Office 365 e do Serviço de Comunicação Azure:

|Domínio registado no Office 365|Exemplos de SBC FQDN em Equipas|Exemplos de nomes SBC FQDN em ACS|
|:--- |:--- |:--- |
**contoso.com** (domínio do segundo nível)|**sbc.contoso.com** (nome no domínio do segundo nível)|**sbc.acs.contoso.com** (nome no domínio do terceiro nível)<br/>**sbc.fabrikam.com** (qualquer nome dentro de diferentes domínios)|
|**o365.contoso.com** (domínio do terceiro nível)|**sbc.o365.contoso.com** (nome no domínio do terceiro nível)|**sbc.contoso.com** (nome no domínio do segundo nível)<br/>**sbc.acs.o365.contoso.com** (nome no domínio do quarto nível)<br/>**sbc.fabrikam.com** (qualquer nome dentro de diferentes domínios)

O emparelhamento SBC funciona ao nível dos recursos dos Serviços de Comunicação, o que significa que pode emparelhar muitos SBCs com um único recurso de Serviços de Comunicação, mas não é possível emparelhar um único SBC com mais de um recurso de Serviços de Comunicação. Os FQDNs SBC exclusivos são necessários para emparelhar com diferentes recursos.

## <a name="public-trusted-certificate-for-the-sbc"></a>Certificado de confiança pública para o SBC

A Microsoft recomenda que solicite o certificado para o SBC gerando um pedido de assinatura de certificação (CSR). Para obter instruções específicas sobre a geração de um RSE para um SBC, consulte as instruções de interconexão ou documentação fornecidas pelos seus fornecedores SBC. 

  > [!NOTE]
  > A maioria das Autoridades de Certificados (AA) exige que o tamanho da chave privada seja pelo menos 2048. Tenha isto em mente ao gerar a RSE.

O certificado deve ter o SBC FQDN como nome comum (CN) ou o nome alternativo sujeito (SAN). O certificado deve ser emitido diretamente a uma autoridade de certificação e não a um prestador intermédio.

Em alternativa, a Interface SIP dos Serviços de Comunicação suporta um wildcard no CN e/ou SAN, e o wildcard precisa de se conformar com o [RFC HTTP padrão SOBRE TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Um exemplo seria usar `\*.contoso.com` o que combinaria com o SBC FQDN, `sbc.contoso.com` mas não combinaria com `sbc.test.contoso.com` .

O certificado deve ser gerado por uma das seguintes autoridades de certificados de raiz:

- AffirmTrust
- Raiz externa de CA AddTrust
- Raiz cybertrust de Baltimore*
- Buypass
- Ciberconfia
- Autoridade de Certificação Primária Pública de Classe 3
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Confiança
- GlobalSign
- Vai papai
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Campo estelar
- Symantec Enterprise Mobile Root para a Microsoft 
- SwissSign
- Descongelamento timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

A Microsoft está a trabalhar na adição de autoridades de certificação adicionais com base em pedidos de clientes. 

## <a name="sip-signaling-fqdns"></a>Sinalização SIP: FQDNs 

Os pontos de ligação para serviços de comunicação SIP Interface são os seguintes três FQDNs:

- **sip.pstnhub.microsoft.com** – Global FQDN – deve ser julgado primeiro. Quando o SBC envia um pedido para resolver este nome, os servidores DNS do Microsoft Azure devolvem um endereço IP que aponta para o centro de dados Azure primário atribuído ao SBC. A atribuição baseia-se nas métricas de desempenho dos datacenters e na proximidade geográfica do SBC. O endereço IP devolvido corresponde ao FQDN primário.
- **sip2.pstnhub.microsoft.com** – FQDN secundário – mapeia geograficamente para a segunda região prioritária.
- **sip3.pstnhub.microsoft.com** – FQDN terciária – mapeia geograficamente para a terceira região prioritária.

A colocação destes três FQDNs em ordem é necessária para:

- Proporcionar uma experiência ótima (menos carregado e mais próximo do datacenter SBC atribuído consultando o primeiro FQDN).
- Fornecer failover quando a ligação de um SBC é estabelecida a um datacenter que está experimentando um problema temporário. Para obter mais informações, consulte [o mecanismo Failover](#failover-mechanism-for-sip-signaling) abaixo.  

As FQDNs – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com e sip3.pstnhub.microsoft.com – serão resolvidas num dos seguintes endereços IP:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Abrir portas de firewall para estes endereços IP para permitir o tráfego de entrada e saída de e para os endereços de sinalização. Se a sua firewall suporta nomes DNS, o FQDN `sip-all.pstnhub.microsoft.com` resolve-se com todos estes endereços IP. 

## <a name="sip-signaling-ports"></a>Sinalização SIP: Portas

Utilize as seguintes portas para serviços de comunicação SIP Interface:

|Trânsito|De|Para|Porta de origem|Porta de destino|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP Proxy|SBC|1024 – 65535|Definido no SBC (para o Office 365 GCC High/DoD apenas deve ser utilizado a porta 5061)|
SIP/TLS|SBC|SIP Proxy|Definido no SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Mecanismo de failover para a sinalização SIP

O SBC faz uma consulta de DNS para resolver sip.pstnhub.microsoft.com. Com base na localização do SBC e nas métricas de desempenho do datacenter, o centro de dados primário é selecionado. Se o centro de dados primário tiver um problema, o SBC tentará o sip2.pstnhub.microsoft.com, que resolve o segundo datacenter atribuído, e, no caso raro de os centros de dados em duas regiões não estarem disponíveis, o SBC retrifica o último FQDN (sip3.pstnhub.microsoft.com), que fornece o picenter de dados terciário.

## <a name="media-traffic-ip-and-port-ranges"></a>Tráfego de mídia: gamas IP e Porto

O tráfego dos meios de comunicação flui de e para um serviço separado chamado Processador de Mídia. No momento da publicação do Processador de Mídia para ACS pode utilizar qualquer endereço IP Azure. Descarregue [a lista completa de endereços.](https://www.microsoft.com/download/details.aspx?id=56519)

### <a name="port-range"></a>Intervalo de portas
A gama de portas dos processadores de mídia é mostrada no quadro seguinte: 

|Trânsito|De|Para|Porta de origem|Porta de destino|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Processador de mídia|SBC|3478-3481 e 49152 - 53247|Definido no SBC|
|UDP/SRTP|SBC|Processador de mídia|Definido no SBC|3478-3481 e 49152 - 53247|

  > [!NOTE]
  > A Microsoft recomenda pelo menos duas portas por chamada simultânea no SBC.


## <a name="media-traffic-media-processors-geography"></a>Tráfego de mídia: Geografia de processadores de mídia

O tráfego dos meios de comunicação flui através de componentes chamados processadores de mídia. Os processadores de mídia são colocados nos mesmos centros de dados que os proxies SIP. Além disso, existem processadores de mídia adicionais para otimizar o fluxo de mídia. Por exemplo, não temos um componente de proxy SIP agora na Austrália (SIP flui via Singapura ou HONG Kong SAR) mas temos o processador de mídia localmente na Austrália. A necessidade dos processadores de meios de comunicação social localmente é ditada pela latência que experimentamos enviando tráfego de longa distância, por exemplo, da Austrália para Singapura ou PARA Hong Kong. Embora a latência no exemplo do tráfego que flui da Austrália para Hong Kong SAR ou Singapura seja aceitável para preservar uma boa qualidade de chamada para o tráfego SIP, para o tráfego mediático em tempo real não é.

Locais onde tanto os componentes do proxy sip como os componentes do processador de mídia foram implantados:
- EUA (dois nos centros de dados do Oeste e do Leste dos EUA)
- Europa (Centros de dados de Amesterdão e Dublin)
- Ásia (Centros de Dados SAR de Singapura e Hong Kong)
- Austrália (centros de dados da AU Leste e Sudeste)

Locais onde apenas são implantados processadores de mídia (fluxos SIP através do centro de dados mais próximo listado acima):
- Japão (centros de dados JP Leste e Oeste)


## <a name="media-traffic-codecs"></a>Tráfego de meios de comunicação: Codecs

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Perna entre o processador SBC e o Cloud Media ou o cliente microsoft Teams.
Aplica-se tanto a casos de bypass mediático como a casos de não bypass.

A interface de encaminhamento direto na perna entre o Controlador de Fronteira da Sessão e o Processador Cloud Media pode utilizar os seguintes códigos:

- SEDA, G.711, G.722, G.729

Pode forçar a utilização do código específico no Controlador de Fronteira da Sessão, excluindo os códigos indesejáveis da oferta.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Perna entre app ACS SDK e processador Cloud Media

Na perna entre o Processador Cloud Media e a app ACS SDK é utilizada SILK ou G.722. A escolha do codec nesta perna baseia-se em algoritmos da Microsoft, que têm em consideração vários parâmetros. 

## <a name="supported-session-border-controllers-sbcs"></a>Controladores de fronteira de sessão suportados (SBCs)

A certificação está em andamento. Entretanto, os clientes podem utilizar [controladores de fronteira de sessão certificada de equipas.](/MicrosoftTeams/direct-routing-border-controllers) 

## <a name="next-steps"></a>Passos seguintes

### <a name="conceptual-documentation"></a>Documentação conceptual

- [Conceito de telefonia](./telephony-concept.md)
- [Tipos de números de telefone nos Serviços de Comunicação Azure](./plan-solution.md)
- [Preços](../pricing.md)

### <a name="quickstarts"></a>Guias de Início Rápido

- [Chamada para telefone](../../quickstarts/voice-video-calling/pstn-call.md)
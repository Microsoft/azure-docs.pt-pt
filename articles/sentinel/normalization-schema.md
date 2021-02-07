---
title: Referência de esquema de normalização de dados Azure Sentinel | Microsoft Docs
description: Este artigo apresenta o esquema de normalização de dados do Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806940"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Referência de esquema de normalização de dados do Azure Sentinel

## <a name="terminology"></a>Terminologia

A seguinte terminologia é utilizada nos esquemas do Azure Sentinel:

| Termo | Definição |
| ---- | ---------- |
| Dispositivo de reporte | O sistema enviando os registos para Azure Sentinel. Pode não ser o sistema de assuntos dos registos. |
| Registo | Uma unidade de dados enviada do dispositivo de reporte. Isto é muitas vezes referido como "log", "evento" ou "alerta", mas não tem necessariamente de ser um desses. |
|

## <a name="data-types-and-formats"></a>Tipos e formatos de dados

Os valores devem ser normalizados com base nas diretrizes abaixo. Isto é obrigatório para campos normalizados e recomendado para outros campos. 

| Tipo de dados | Tipo físico | Formato e valor |
| --------- | ------------- | ---------------- |
| **Data/Hora** | Dependendo da utilização da capacidade do método de ingestão na prioridade descendente:<ul><li>Log Analytics incorporado no tipo de data</li><li>Um campo inteiro usando log analytics representação numérica da data</li><li>Um campo de cordas usando a representação numérica da data de data do Log Analytics</li></ul> | Registar representação da hora de data do Log Analytics. <br></br>Log Analytics data & a representação do tempo é de natureza semelhante, mas diferente da representação do tempo Unix. Consulte estas diretrizes de conversão. <br></br>A data & hora deve ser ajustada. |
| **Endereço MAC** | String | notação Colon-Hexadecimal |
| **Endereço IP** | Endereço IP | O esquema não tem endereços IPv4 e IPv6 separados. Qualquer campo de endereço IP pode incluir um endereço IPv4 ou um endereço IPv6:<ul><li>IPv4 em notação de ponto-decimal</li><li>IPv6 em 8 hextets notação, permitindo as formas curtas descritas aqui.</li></ul> |
| **Utilizador** | String | Estão disponíveis os seguintes 3 campos de utilizadores:<ul><li>Nome de utilizador</li><li>UPN de utilizador</li><li>Domínio do utilizador</li></ul> |
| **ID de Utilizador** | String | Os seguintes 2 IDs de utilizador são atualmente suportados:<ul><li>SID do utilizador</li><li>ID de diretório Azure Ative</li></ul> |
| **Dispositivo** | String | São suportadas as seguintes colunas de dispositivo/hospedeiro:<ul><li>ID</li><li>Name</li><li>Nome de domínio totalmente qualificado (FQDN)</li></ul> |
| **País** | String | Uma corda usando ISO 3166-1 de acordo com esta prioridade:<ul><li>Códigos Alfa-2 (isto é, EUA para os Estados Unidos)</li><li>Códigos Alfa-3 (isto é, EUA para os Estados Unidos)</li><li>Nome abreviado</li></ul> |
| **Região** | String | O nome da sub-divisão do país usando ISO 3166-2 |
| **City** | String | |
| **Longitude** | Double (Duplo) | Representação coordenada ISO 6709 (decimal assinado) |
| **Latitude** | Double (Duplo) | Representação coordenada ISO 6709 (decimal assinado) |
| **Algoritmo de haxixe** | String | São suportadas as seguintes colunas de haxixe:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Tipo de Arquivo** | String | O tipo do tipo de ficheiro:<ul><li>Extensão</li><li>Classe</li><li>ChamadoType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Esquema de tabela de sessões de rede

Abaixo está o esquema da tabela de sessões de rede, versão 1.0.0

| Nome do campo | Tipo de valor | Exemplo | Descrição | Entidades associadas da OSSEM |
|-|-|-|-|-|
| EventType | String | Trânsito | Tipo de evento a ser recolhido | Evento |
| EventSubType | String | Autenticação | Descrição adicional do tipo, se aplicável | Evento |
| Conta de Eventos | Número inteiro  | 10 | O número de eventos agregados, se aplicável. | Evento |
| EventEndTime | Data/Hora | Ver "tipos de dados" | O tempo em que o evento terminou | Evento |
| EventoSMessage | string |  acesso negado | Uma mensagem geral ou descrição, incluída ou gerada a partir do registo | Evento |
| DvcIpAddr | Endereço IP |  23.21.23.34 | O endereço IP do dispositivo que gera o registo | Dispositivo,<br>IP |
| DvcMacAddr | String | 06:10:9f:eb:8f:14 | O endereço MAC da interface de rede do dispositivo de reporte a partir do qual o evento foi enviado. | Dispositivo,<br>Mac |
| DvcHostname | Nome do dispositivo (cadeia) | syslogserver1.contoso.com | O nome do dispositivo do dispositivo que gera a mensagem. | Dispositivo |
| Produto de Eventos | String | OfficeSharepoint | O produto que gera o evento. | Evento |
| EventProductVersion | string | 9.0 |  A versão do produto que gera o evento. | Evento |
| EventResourceId | ID do dispositivo (cadeia) | /subscrições/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | O ID de recursos do dispositivo que gera a mensagem. | Evento |
| EventReportUrl | String | https://192.168.1.1/repoerts/ae3-56.htm | Um link para o relatório completo criado pelo dispositivo de reporte | Evento |
| EventVendor | String | Microsoft | O fornecedor do produto que gera o evento. | Evento |
| EventoResult | Multivalue: Sucesso, Parcial, Fracasso, [Vazio] (Cadeia) | Com êxito | O resultado reportado para a atividade. Valor vazio quando não aplicável. | Evento |
| EventResultDetails | String | Senha errada | Razão ou detalhes para o resultado relatado no EventResult | Evento |
| EventoSsAversão | Real | 0.1 | Versão Azure Sentinel Schema. Atualmente 0.1. | Evento |
| EventSeverity | String | Baixo | Se a atividade reportada tiver um impacto de segurança, denota a gravidade do impacto. | Evento |
| EventoOriginaluid | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | A identificação do registo do dispositivo de reporte. | Evento |
| EventStartTime | Data/Hora | Ver "tipos de dados" | O tempo em que o evento declarou | Evento |
| TimeGenerated | Data/Hora | Ver "tipos de dados" | A hora do evento ocorreu, como relatou fonte. | Campo personalizado |
| EventTimeIngested | Data/Hora | Ver "tipos de dados" | O momento em que o evento foi ingerido para Azure Sentinel. Será adicionado por Azure Sentinel. | Evento |
| EventUid | Guid (Corda) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Identificador único usado por Azure Sentinel para marcar uma linha. | Evento |
| NetworkApplicationProtocol | String | HTTPS | O protocolo de camada de aplicação utilizado pela ligação ou sessão. | Rede |
| DstBytes | int | 32455 | O número de bytes enviados do destino para a fonte para a ligação ou sessão. | Destino |
| SrcBytes | int | 46536 | O número de bytes enviados da fonte para o destino para a ligação ou sessão. | Origem |
| NetworkBytes | int | 78991 | Número de bytes enviados em ambas as direções. Se tanto BytesReceived como BytesSent existem, bytesTotal deve igualar a sua soma. | Rede |
| NetworkDirection | Multi-valor: Entrada, Saída (cadeia) | Entrada | A direção da ligação ou sessão, dentro ou fora da organização. | Rede |
| DstGeoCity | String | Burlington | A cidade associada ao endereço IP de destino | Destino,<br>Área Geográfica |
| DstGeoCountry | País (Corda) | E.U.A. | O país associado ao endereço IP de origem | Destino,<br>Área Geográfica |
| Nome DstDvcHostname | Nome do dispositivo (String) |  victim_pc | O nome do dispositivo do destino | Destino<br>Dispositivo |
| DstDvcFqdn | String | victim_pc.contoso.local | O nome de domínio totalmente qualificado do anfitrião onde o log foi criado | Destino,<br>Dispositivo |
| DstDomainHostname | string | CONTOSO | O domínio do destino, o domínio do anfitrião de destino (website, nome de domínio, etc.), por exemplo, para pesquisas de DNS ou pesquisas NS | Destino |
| Nome DstInterface | string | Adaptador de rede Hiper-V da Microsoft | A interface de rede utilizada para a ligação ou sessão pelo dispositivo de destino. | Destino |
| DstInterfaceGuid | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUIA da interface de rede que foi utilizada para pedido de autenticação  | Destino |
| DstIpAddr | Endereço IP | 2001:db8::ff00:42:8329 | O endereço IP da ligação ou destino da sessão, mais comumente referido como o IP de destino no pacote de rede | Destino,<br>IP |
| DstDvcIpAddr | Endereço IP | 75.22.12.2 | O endereço IP de destino de um dispositivo que não está diretamente associado ao pacote de rede | Destino,<br>Dispositivo,<br>IP
| DstGeoLatitude | Latitude (Duplo) | 44.475833 | A latitude da coordenada geográfica associada ao endereço IP de destino | Destino,<br>Área Geográfica |
| DstMacAddr | String | 06:10:9f:eb:8f:14 | O endereço MAC da interface de rede em que a ligação ou sessão terminou, mais comumente referido ao destino MAC no pacote de rede | Destino,<br>MAC |
| DstDvcMacAddr | String | 06:10:9f:eb:8f:14 | O endereço MAC de destino de um dispositivo que não está diretamente associado ao pacote de rede. | Destino,<br>Dispositivo,<br>MAC |
| DstDvcDomain | String | CONTOSO | O Domínio do dispositivo de destino. | Destino,<br>Dispositivo |
| DstPortNumber | Número inteiro | 443 | A porta IP de destino. | Destino,<br>Porta |
| Região de DstGeo | Região (Corda) | Vermont | A região dentro de um país associado ao endereço IP de destino | Destino,<br>Área Geográfica |
| DstResourceId | ID do dispositivo (cadeia) |  /subscrições/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /vítima | O id de recursos do dispositivo de destino. | Destino |
| DstNatIpAddr | Endereço IP | 2::1 | Se reportado por um dispositivo NAT intermediário, como uma firewall, o endereço IP utilizado pelo dispositivo NAT para comunicação com a fonte. | Destino NAT,<br>IP |
| DstNatPortNumber | int | 443 | Se for reportado por um dispositivo NAT intermediário, como uma firewall, a porta utilizada pelo dispositivo NAT para comunicação com a fonte. | Destino NAT,<br>Porta |
| DstUserSid | SID do utilizador |  S-12-1445 | Identificação do Utilizador da identidade associada ao destino da sessão. Tipicamente, a identidade usada para autenticar um servidor. Consulte "tipos de dados" para obter mais detalhes. | Destino,<br>User |
| DstUserAadId | Corda (guia) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | O ID do objeto de conta Azure AD do utilizador no final da sessão | Destino,<br>User |
| Nome DstUser | Nome de utilizador (String) | johnd | O nome de utilizador da identidade associada ao destino da sessão.  | Destino,<br>User |
| DstUserUpn | string | johnd@anon.com | A UPN da identidade associada ao destino da sessão. | Destino,<br>User |
| DstUserDomain | string | GRUPO DE TRABALHO | O domínio ou nome do computador da conta no destino da sessão | Destino,<br>User |
| DstZone | String | Dmz | A zona de rede do destino, tal como definida pelo dispositivo de reporte. | Destino |
| DstGeoLongitude | Longitude (Duplo) | -73.211944 | A longitude da coordenada geográfica associada ao endereço IP de destino | Destino,<br>Área Geográfica |
| DvcAction | Multi-valor: Permitir, Negar, Largar (cadeia) | Permitir | Se reportado por um dispositivo intermediário, como uma firewall, a ação tomada pelo dispositivo. | Dispositivo |
| DvcInboundInterface | String | eth0 | Se for reportado por um dispositivo intermediário, como uma firewall, a interface de rede utilizada por ele para a ligação ao dispositivo de origem. | Dispositivo |
| DvcOutboundInterface | String  | Adaptador Ethernet Ethernet 4 | Se reportado por um dispositivo intermediário, como uma firewall, a interface de rede por si utilizada para a ligação ao dispositivo de destino. | Dispositivo |
| NetworkDuration | Número inteiro | 1500 | O tempo, em milissegundo, para a conclusão da sessão de rede ou ligação | Rede |
| Código de RedeIcmp | Número inteiro | 34 | Para uma mensagem ICMP, o valor numérico do tipo de mensagem ICMP (RFC 2780 ou RFC 4443). | Rede |
| RedeIcmpType | String | Destino Inacessível | Para uma mensagem ICMP, representação de texto tipo icMP (RFC 2780 ou RFC 4443). | Rede |
| DstPackets | int  | 446 | O número de pacotes enviados do destino para a fonte para a ligação ou sessão. O significado de um pacote é definido pelo dispositivo de reporte. | Destino |
| SrcPackets | int  | 6478 | O número de pacotes enviados da fonte para o destino para a ligação ou sessão. O significado de um pacote é definido pelo dispositivo de reporte. | Origem |
| Pacotes de rede | int  | 0 | Número de pacotes enviados em ambas as direções. Se ambos os PacotesReceived e PacketsSent existirem, o BytesTotal deve igualar a sua soma. | Rede |
| HttpRequestTime | Número inteiro | 700 | O tempo que demorou a enviar o pedido para o servidor, se aplicável. | Http |
| HttpResponseTime | Número inteiro | 800 | O tempo que demorou a receber uma resposta no servidor, se aplicável. | Http |
| Nome de rederule | String | AnyAnyDrop | O nome ou ID da regra pela qual o DeviceAction foi decidido | Rede |
| NetworkRuleNumber | int |  23 | Número de regras combinado  | Rede |
| NetworkSessionId | string | 172_12_53_32_4322__123_64_207_1_80 | O identificador da sessão foi relatado pelo dispositivo de reporte. Por exemplo, identificador de sessão L7 para aplicações específicas após a autenticação | Rede |
| SrcGeoCity | String | Burlington | A cidade associada com o endereço IP de origem | Fonte,<br>Área Geográfica |
| SrcGeoCountry | País (Corda) | E.U.A. | O país associado ao endereço IP de origem | Fonte,<br>Área Geográfica |
| SrcDvcHostname | Nome do dispositivo (String) |  vilão | O nome do dispositivo de origem | Fonte,<br>Dispositivo |
| SrcDvcFqdn | string | Villain.malicious.com | O nome de domínio totalmente qualificado do anfitrião onde o log foi criado | Fonte,<br>Dispositivo |
| SrcDvcDomain | string | EVILORG | Domínio do dispositivo a partir do qual foi iniciada a sessão | Fonte,<br>Dispositivo |
| SrcDvcOs | String | iOS | O SO do dispositivo de origem | Fonte,<br>Dispositivo |
| SrcDvcModelName | String | Samsung Galaxy Note | O nome modelo do dispositivo de origem | Fonte,<br>Dispositivo |
| SrcDvcModelNumber | String | 10,0 | O número de modelo do dispositivo de origem | Fonte,<br>Dispositivo |
| SrcDvcType | String | Móvel | O tipo do dispositivo de origem | Fonte,<br> Dispositivo |
| SrcIntefaceName | String | eth01 | A interface de rede utilizada para a ligação ou sessão pelo dispositivo de origem. | Origem |
| SrcInterfaceGuid | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUIA da interface de rede utilizada | Origem |
| SrcIpAddr | Endereço IP | 77.138.103.108 | O endereço IP a partir do qual a ligação ou sessão teve origem. | Fonte,<br>IP |
| SrcDvcIpAddr | Endereço IP | 77.138.103.108 | O endereço IP de origem de um dispositivo não diretamente associado ao pacote de rede (recolhido por um fornecedor ou explicitamente calculado). | Fonte,<br>Dispositivo,<br>IP |
| SrcGeoLatitude | Latitude (Duplo) | 44.475833 | A latitude da coordenada geográfica associada ao endereço IP de origem | Fonte,<br>Área Geográfica |
| SrcGeoLongitude | Longitude (Duplo) | -73.211944 | A longitude da coordenada geográfica associada ao endereço IP de origem | Fonte,<br>Área Geográfica |
| SrcMacAddr | String | 06:10:9f:eb:8f:14 | O endereço MAC da interface de rede a partir da qual originou a sessão de od de ligação. | Fonte,<br>Mac |
| SrcDvcMacAddr | String | 06:10:9f:eb:8f:14 | O endereço MAC de origem de um dispositivo que não está diretamente associado ao pacote de rede. | Fonte,<br>Dispositivo,<br>Mac |
| SrcPortNumber | Número inteiro | 2335 | A porta IP de onde a ligação se originou. Pode não ser relevante para uma sessão que inclua múltiplas ligações. | Fonte,<br>Porta |
| SrcGeoRegion | Região (Corda) | Vermont | A região dentro de um país associado ao endereço IP de origem | Fonte,<br>Área Geográfica |
| SrcResourceId | String | /subscrições/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | O ID de recursos do dispositivo que gera a mensagem. | Origem |
| SrcNatIpAddr | Endereço IP | 4.3.2.1 | Se reportado por um dispositivo NAT intermediário, como uma firewall, o endereço IP utilizado pelo dispositivo NAT para comunicação com o destino. | FONTE NAT,<br>IP |
| SrcNatPortNumber | Número inteiro | 345 | Se reportado por um dispositivo NAT intermediário, como uma firewall, a porta utilizada pelo dispositivo NAT para comunicação com o destino. | FONTE NAT,<br>Porta |
| SrcUserSid | ID do utilizador (cadeia) | S-15-1445 | Identificação do utilizador da identidade associada à fonte das sessões. Normalmente, o utilizador realiza uma ação sobre o cliente. Consulte "tipos de dados" para obter mais detalhes. | Fonte,<br>User |
| SrcUserAadId | Corda (guia) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | O ID do objeto de conta Azure AD do utilizador no final da sessão | Fonte,<br>User |
| SrcUserName | Nome de utilizador (String) | bob | O nome de utilizador da identidade associada à fonte das sessões. Normalmente, o utilizador realiza uma ação sobre o cliente. Consulte "tipos de dados" para obter mais detalhes. | Origem<br>User |
| SrcUserUpn | string | bob@alice.com | UPN da conta que inicia a sessão | Fonte,<br>User |
| SrcUserDomain | string | DESKTOP | O domínio da conta que inicia a sessão | Fonte,<br>User |
| SrcZone | String | Toque | A zona de rede da fonte, tal como definida pelo dispositivo de reporte. | Origem |
| NetworkProtocol | String | TCP | O protocolo IP utilizado pela ligação ou sessão. Tipicamente, TCP, UDP ou ICMP | Rede |
| CloudAppName | String | Facebook | O nome do pedido de destino para um pedido HTTP identificado por um representante. | Cloud |
| CloudAppId | String | 124 | O ID do pedido de destino para uma aplicação HTTP identificada por um representante. Este valor é geralmente específico do proxy utilizado. | Cloud |
| CloudAppOperation | String | DeleteFile | A operação que o utilizador realizou no contexto do pedido de destino para uma aplicação HTTP identificada por um representante. Este valor é geralmente específico do proxy utilizado. | Cloud |
| CloudAppRiskLevel | String | 3 | O nível de risco associado a uma aplicação HTTP identificada por um representante. Este valor é geralmente específico do proxy utilizado. | Cloud |
| Nome de arquivo | String | ImNotMalicious.exe | O nome de ficheiro transmitido através das ligações de rede para protocolos como FTP e HTTP que fornecem a informação sobre o nome do ficheiro. | Ficheiro |
| FilePath | String | C:\Malicious\ImNotMalicious.exe | O caminho completo, incluindo o nome do ficheiro, do ficheiro | Ficheiro |
| FileHashMd5 | String | 51BC68715FC7C109DCEA406B42D9D78F | O valor hash MD5 do ficheiro transmitido através das ligações de rede para protocolos. | Ficheiro |
| Filehashsha1 | String | 491AE3... C299821476F4 | O valor hash sha1 do ficheiro transmitido através das ligações de rede para protocolos. | Ficheiro |
| Filehashsha256 | String | 9B8F8EDB... C129976F03 | O valor hash sha256 do ficheiro transmitido através das ligações de rede para protocolos. | Ficheiro |
| Filehashsha512 | String | 5E127D... F69F73F01F361 | O valor hash sha512 do ficheiro transmitido através das ligações de rede para protocolos. | Ficheiro |
| Arquidestésia |  String | exe | O tipo de ficheiro transmitido através das ligações de rede para protocolos como FTP e HTTP. | Ficheiro
| FileMimeType | String | aplicação/msword | O tipo MIME do ficheiro transmitido através das ligações de rede para protocolos como FTP e HTTP | Ficheiro |
| Tamanho de arquivo | Número inteiro | 23500 | O tamanho do ficheiro, em bytes, do ficheiro transmitido através das ligações de rede para protocolos. | Ficheiro |
| HttpVersion | String | 2.0 | A versão http request para ligações de rede HTTP/HTTPS. | Http |
| HttpRequestMethod | String | GET | O método HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpStatusCode | String | 404 | O código de estado HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpContentType | String | multiparte/formulários; limite=algo | O cabeçalho do tipo de conteúdo http response para sessões de rede HTTP/HTTPS. | Http |
| HttpReferrerOriginal | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | O cabeçalho de remetente http para sessões de rede HTTP/HTTPS. | Http |
| HttpUserAgentOriginal | String | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, como Gecko) Chrome/83.0.4103.97 Safari/537.36 | O cabeçalho do agente http para sessões de rede HTTP/HTTPS. | Http |
| HttpRequestXff | String | 120.12.41.1 | O cabeçalho http-x-forward-for para sessões de rede HTTP/HTTPS. | Http |
| UrlCategoria | String | Motores de busca | O agrupamento definido de um URL (ou pode ser apenas baseado no domínio no URL) relacionado com o que é (por assim dizer: adulto, notícias, publicidade, domínios estacionados, etc.) | url |
| UrlOriginal | String | https:// contoso.com/fo/?k=v&q=u#f | O URL de pedido http para sessões de rede HTTP/HTTPS. | Url |
| UrlHostname | String | contoso.com | A parte de domínio de um URL de pedido HTTP para sessões de rede HTTP/HTTPS. | Url |
| AmeaçaCategoria | String | Troia | A categoria de uma ameaça identificada por um sistema de segurança como o Portal de Segurança Web de um IPS e está associada a esta sessão de rede. | Ameaça |
| ThreatId | String | Tr.124 | O ID de uma ameaça identificada por um sistema de segurança como o Portal de Segurança Web de um IPS e está associada a esta sessão de rede. | Ameaça |
| Nome de ameaça | String | Ficheiro de Teste EICAR | O nome da ameaça ou malware identificado | Ameaça |
| Campos Adicionais | Dinâmico (saco JSON) | {<br>Propriedade1: "val1",<br>Propriedade2: "val2"<br>} | Quando nenhuma coluna respetiva no esquema corresponder, campos adicionais podem ser guardados num saco JSON.<br>Para a análise do tempo de consulta, recomenda-se não utilizar este método como dados de embalagem num JSON irá degradar o desempenho da consulta. Em vez disso, recomenda-se promover colunas adicionais.<br>Para futuros cenários de análise do tempo de ingestão, os dados adicionais serão, naturalmente, recolhidos nesta coluna de sacos JSON. | Campo personalizado |
| 

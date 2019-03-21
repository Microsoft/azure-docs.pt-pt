---
title: Acerca de dispositivos VPN para ligações do Azure entre locais | Microsoft Docs
description: Este artigo aborda os dispositivos VPN e os parâmetros IPsec para ligações entre locais do Gateway de VPN S2S. São fornecidas ligações para obter instruções e exemplos de configuração.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: yushwang
ms.openlocfilehash: 188412130b059cd25952ce9bf570c4e95ebbc43a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993411"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Acerca dos dispositivos de VPN e dos parâmetros IPsec/IKE para ligações do Gateway da Rede de VPNs

Precisa de um dispositivo VPN para configurar uma ligação de rede de VPNs (S2S) entre locais através de um gateway de VPN. As ligações Site a Site podem ser utilizadas para criar uma solução híbrida ou sempre que pretender ligações seguras entre redes no local e redes virtuais. Este artigo fornece uma lista de dispositivos VPN validados e uma lista de parâmetros de IPsec/IKE para gateways de VPN.

> [!IMPORTANT]
> Se ocorrerem problemas de conectividade entre os dispositivos VPN no local e gateways de VPN, veja [Problemas de compatibilidade de dispositivos conhecidos](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Itens a ter em atenção quando visualizar as tabelas:

* A terminologia para os gateways de VPN do Azure foi alterada. Apenas os nomes foram alterados. Não há qualquer alteração de funcionalidade.
  * Encaminhamento Estático = PolicyBased
  * Encaminhamento Dinâmico = RouteBased
* As especificações para o gateway de VPN HighPerformance e para o gateway de VPN RouteBased são as mesmas, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados compatíveis com os gateways de VPN RouteBased também são compatíveis com o gateway de VPN HighPerformance.

## <a name="devicetable"></a>Dispositivos VPN validados e guias de configuração de dispositivo

> [!NOTE]
> Ao configurar uma ligação Site a Site, é preciso um endereço IP IPv4 destinado ao público para o dispositivo VPN.
>

Em parceria com os fornecedores dos dispositivos, validámos uma série de dispositivos VPN padrão. Todos os dispositivos nas famílias de dispositivos na lista seguinte deverão funcionar com gateways de VPN. Veja [Acerca das definições do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype) para compreender a utilização do tipo de VPN (PolicyBased ou RouteBased) para a solução do Gateway de VPN que quer configurar.

Para ajudar a configurar o dispositivo VPN, consulte as ligações que correspondem à família de dispositivos adequada. As ligações para as instruções de configuração são fornecidas numa base de melhor esforço. Para obter suporte para dispositivos VPN, contacte o fabricante do dispositivo.

|**Fornecedor**          |**Família de dispositivos**     |**Versão mínima do SO** |**Instruções de configuração PolicyBased** |**Instruções de configuração RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Não compatível  |[Guia de configuração](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |Routers VPN Série AR |Série AR 5.4.7+               |Brevemente     |[Guia de configuração](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guia de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Guia de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série X |Barracuda Firewall 6.5 |[Guia de configuração](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Não compatível |
| Brocade            |Vyatta 5400 vRouter   |Virtual Router 6.6R3 GA|[Guia de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Não compatível |
| Check Point |Gateway de Segurança |R80.10 |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Suportadas |[Guia de configuração*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Suportadas |Suportadas |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Suportadas |Suportadas |
| Cisco |Meraki |N/A |Não compatível |Não compatível |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e posterior |[Guia de configuração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Não compatível |
| F5 |Série BIG-IP |12.0 |[Guia de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guia de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Guia de configuração](https://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guia de configuração](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Não compatível |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Suportadas |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Suportadas |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Suportadas |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Suportadas |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Suportadas |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Serviço de Encaminhamento e Acesso Remoto |Windows Server 2012 |Não compatível |Suportadas |
| Open Systems AG |Gateway de Segurança do Controlo da Missão |N/A |[Guia de configuração](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Não compatível |
| Palo Alto Networks |Todos os dispositivos com o PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 ou posterior<br>RouteBased: 7.1.4 |[Guia de configuração](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Guia de configuração](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| ShareTech | UTM de Próxima Geração (série NU) | 9.0.1.3 | Não compatível | [Guia de configuração](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Série TZ, Série NSA<br>Série SuperMassive<br>Série NSA Classe E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Não compatível |[Guia de configuração](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Firewall de Próxima Geração XG | XG v17 | | [Guia de configuração](https://community.sophos.com/kb/127546)<br><br>[Guia de configuração - vários SAs](https://community.sophos.com/kb/en-us/133154) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 |  | [BGP através de IPsec do IKEv2](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI através de IPsec do IKEv2](https://help.ubnt.com/hc/en-us/articles/115012305347)
| WatchGuard |Todos |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) As versões 8.4+ do Cisco ASA adicionam suporte para IKEv2 e permitem ligar ao gateway de VPN do Azure através da política IPsec/IKE personalizada com a opção "UsePolicyBasedTrafficSelectors". Veja este [artigo de procedimentos](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\*\*) Os routers da Série ISR 7200 só suportam VPNs PolicyBased.

## <a name="configscripts"></a>Transfere os scripts de configuração do dispositivo VPN do Azure

Para determinados dispositivos, pode transferir os scripts de configuração diretamente a partir do Azure. Para obter mais informações e a transferência instruções, consulte [scripts de configuração de dispositivo de VPN transferir](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Dispositivos com scripts de configuração disponíveis

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="additionaldevices"></a>Dispositivos VPN não validados

Se não vir o seu dispositivo listado na tabela de Dispositivos VPN Validados, o seu dispositivo poderá, mesmo assim, funcionar com uma ligação Site a Site. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.

## <a name="editing"></a>Editar os exemplos de configuração do dispositivo

Depois de transferir o exemplo de configuração do dispositivo VPN fornecido, terá de substituir alguns dos valores para que reflitam as definições do seu ambiente.

### <a name="to-edit-a-sample"></a>Para editar um exemplo:

1. Abra o exemplo com o Bloco de Notas.
2. Procure e substitua todas as cadeias <*texto*> pelos valores que dizem respeito ao seu ambiente. Não se esqueça de incluir < e >. O nome especificado deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo** | **Alterar para** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |O nome que escolheu para este objeto. Exemplo: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |O nome que escolheu para este objeto. Exemplo: myAzureNetwork |
| &lt;RP_AccessList&gt; |O nome que escolheu para este objeto. Exemplo: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |O nome que escolheu para este objeto. Exemplo: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |O nome que escolheu para este objeto. Exemplo: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Especifique o intervalo. Exemplo: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Especifique a máscara de sub-rede. Exemplo: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Especifique o intervalo no local. Exemplo: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Especifique a máscara de sub-rede no local. Exemplo: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Esta informação é específica da sua rede virtual e encontra-se no Portal de Gestão como **Endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt; |Esta informação é específicas da sua rede virtual e encontra-se no Portal de Gestão como Gerir Chave. |

## <a name="ipsec"></a>Parâmetros de IPsec/IKE

> [!IMPORTANT]
> 1. As seguintes tabelas contêm as combinações de algoritmos e parâmetros que os gateways de VPN do Azure utilizam na configuração predefinida. Para gateways de VPN baseados em rota criados com o modelo de implementação da Gestão de Recursos do Azure, pode especificar uma política personalizada em cada ligação individual. Veja o artigo [Configurar a política IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções detalhadas.
>
> 2. Além disso, tem de fixar o TCP **MSS** em **1350**. Se os dispositivos VPN não suportarem a afixação MSS, pode, em alternativa, definir o **MTU** na interface de túnel para **1400** bytes.
>

Nas seguintes tabelas:

* SA = Associação de Segurança
* IKE Fase 1 também é denominado "Modo Principal"
* IKE Fase 2 também é denominado "Modo Rápido"

### <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros de IKE Fase 1 (Modo Principal)

| **Propriedade**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Versão do IKE           |IKEv1              |IKEv2              |
| Grupo Diffie-Hellman  |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de Autenticação |Chave Pré-partilhada     |Chave Pré-partilhada     |
| Algoritmos de Encriptação e Hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Duração de SA           |28 800 segundos     |28 800 segundos     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parâmetros de IKE Fase 2 (Modo Rápido)

| **Propriedade**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Versão do IKE                   |IKEv1          |IKEv2                                        |
| Algoritmos de Encriptação e Hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Ofertas de RouteBased QM SA](#RouteBasedOffers) |
| Duração de SA (Tempo)            |3600 segundos  |27,000 segundos                                |
| Duração de SA (Bytes)           |102 400 000 KB | -                                           |
| Perfect Forward Secrecy (PFS) |Não             |[Ofertas de RouteBased QM SA](#RouteBasedOffers) |
| Deteção de Elemento Inutilizado (DPD)     |Não suportado  |Suportadas                                    |


### <a name ="RouteBasedOffers"></a>Ofertas de RouteBased VPN IPsec Security Association (SA do Modo Rápido de IKE)

A tabela seguinte apresenta as Ofertas de SA IPsec (Modo Rápido de IKE). As ofertas estão listadas pela ordem de preferência com que a oferta é apresentada ou aceite.

#### <a name="azure-gateway-as-initiator"></a>Gateway do Azure como iniciador

|-  |**Encriptação**|**Autenticação**|**Grupo PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhuma         |
| 2 |AES256        |SHA1              |Nenhuma         |
| 3 |3DES          |SHA1              |Nenhuma         |
| 4 |AES256        |SHA256            |Nenhuma         |
| 5 |AES128        |SHA1              |Nenhuma         |
| 6 |3DES          |SHA256            |Nenhuma         |

#### <a name="azure-gateway-as-responder"></a>Gateway do Azure como dispositivo de resposta

|-  |**Encriptação**|**Autenticação**|**Grupo PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhuma         |
| 2 |AES256        |SHA1              |Nenhuma         |
| 3 |3DES          |SHA1              |Nenhuma         |
| 4 |AES256        |SHA256            |Nenhuma         |
| 5 |AES128        |SHA1              |Nenhuma         |
| 6 |3DES          |SHA256            |Nenhuma         |
| 7 |DES           |SHA1              |Nenhuma         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Nenhuma         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Pode especificar a encriptação IPsec ESP NULL com gateways de VPN RouteBased e HighPerformance. A encriptação baseada em Nulo não proporciona proteção aos dados em trânsito e só deve ser utilizada quando é preciso ter débito máximo e latência mínima. Os clientes podem optar por utilizar esta opção em cenários de comunicação de VNet a VNet ou quando a encriptação está a ser aplicada noutro ponto da solução.
* Para a conectividade em vários locais através da Internet, utilize as predefinições do gateway de VPN do Azure com a encriptação e os algoritmos hash listados nas tabelas acima, para garantir a segurança da sua comunicação crítica.

## <a name="known"></a>Problemas de compatibilidade de dispositivos conhecidos

> [!IMPORTANT]
> Estes são os problemas de compatibilidade conhecidos entre dispositivos VPN de terceiros e gateways de VPN do Azure. A equipa do Azure está a trabalhar ativamente com os fornecedores para resolver os problemas aqui listados. Assim que os problemas são resolvidos, esta página será atualizada com as informações mais atualizadas. Volte a verificar periodicamente.
>
>

### <a name="feb-16-2017"></a>16 de fevereiro de 2017

**Dispositivos da Palo Alto Networks com versão anterior à 7.1.4** para VPN baseado na rota do Azure: Se estiver a utilizar dispositivos da Palo Alto Networks com versão do SO PAN anterior à 7.1.4 e ocorrerem problemas de conectividade para gateways VPN baseados em rota do Azure, execute os seguintes passos:

1. Verifique a versão de firmware do seu dispositivo da Palo Alto Networks. Se a sua versão do SO PAN for anterior à 7.1.4, atualize para 7.1.4.
2. No dispositivo da Palo Alto Networks, altere a duração da Fase 2 SA (ou SA de Modo Rápido) para 28,800 segundos (8 horas) quando ligar ao gateway de VPN do Azure.
3. Se ainda ocorrerem problemas de conectividade, abra um pedido de suporte a partir do portal do Azure.

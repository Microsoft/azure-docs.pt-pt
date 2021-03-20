---
title: 'Azure VPN Gateway: Sobre dispositivos VPN para ligações'
description: Este artigo aborda os dispositivos VPN e os parâmetros IPsec para ligações entre locais do Gateway de VPN S2S. São fornecidas ligações para obter instruções e exemplos de configuração.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 4c6bd62e96d85305036626a8672c39ff1b9f6b26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201098"
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

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>Dispositivos VPN validados e guias de configuração de dispositivo

Em parceria com os fornecedores dos dispositivos, validámos uma série de dispositivos VPN padrão. Todos os dispositivos nas famílias de dispositivos na lista seguinte deverão funcionar com gateways de VPN. Veja [Acerca das definições do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype) para compreender a utilização do tipo de VPN (PolicyBased ou RouteBased) para a solução do Gateway de VPN que quer configurar.

Para ajudar a configurar o seu dispositivo VPN, consulte os links que correspondem à família do dispositivo apropriado. As ligações para as instruções de configuração são fornecidas numa base de melhor esforço. Para obter suporte para dispositivos VPN, contacte o fabricante do dispositivo.

|**Fornecedor**          |**Família de dispositivos**     |**Versão mínima do SO** |**Instruções de configuração PolicyBased** |**Instruções de configuração RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Não compatível  |[Guia de configuração](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |Routers VPN Série AR |AR-Series 5.4.7+               | [Guia de configuração](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Guia de configuração](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Rio Arista | CloudEOS Router | vEOS 4.24.0FX | (não testado) | [Guia de configuração](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen Firewall |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guia de configuração](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Guia de configuração](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Gateway de Segurança |R80.10 |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Suportado |[Guia de configuração*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Suportado |Suportado |
| Cisco | CSR | Percurso: IOS-XE 16.10 | (não testado) | [Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Suportado |Suportado |
| Cisco |Meraki (MX) | MX v15.12 |Não compatível | [Guia de configuração](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela OS) | 18.4.0 (Modo Ativo/Passivo)<br><br>19.2 (Modo Ativo/Ativo) | Não compatível |  [Configuração manual (Ativa/Passiva)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Configuração onramp de nuvem (Ativo/Ativo)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e posterior |[Guia de configuração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Não compatível |
| F5 |Série BIG-IP |12.0 |[Guia de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guia de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (não testado) |[Guia de configuração](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Redes Hillstone | Firewalls de próxima geração (NGFW) | 5.5R7  | (não testado) | [Guia de configuração](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guia de configuração](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Não compatível |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Suportado |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Suportado |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Suportado |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Suportado |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Suportado |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Serviço de Encaminhamento e Acesso Remoto |Windows Server 2012 |Não compatível |Suportado |
| Open Systems AG |Gateway de Segurança do Controlo da Missão |N/D |[Guia de configuração](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |Não compatível |
| Palo Alto Networks |Todos os dispositivos com o PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 ou posterior<br>RouteBased: 7.1.4 |Suportado |[Guia de configuração](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (Desenvolvedor) | VyOS | VyOS 1.2.2 | (não testado) | [Guia de configuração ](https://docs.vyos.io/en/latest/configexamples/azure-vpn-bgp.html)|
| ShareTech | UTM de Próxima Geração (série NU) | 9.0.1.3 | Não compatível | [Guia de configuração](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Série TZ, Série NSA<br>Série SuperMassive<br>Série NSA Classe E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Não compatível |[Guia de configuração](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Firewall de Próxima Geração XG | XG v17 | (não testado) | [Guia de configuração](https://community.sophos.com/kb/127546)<br><br>[Guia de configuração - Várias SAs](https://community.sophos.com/kb/en-us/133154) |
| Sinologia | MR2200ac <br>RT2600ac <br>RT1900ac | SRM1.1.5/VpnPlusServer-1.2.0 | (não testado) | [Guia de configuração](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | (não testado) | [BGP sobre IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI sobre IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0.T3 Build-13  | (não testado) | Guia de configuração |
| WatchGuard |Todos |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |Série USG ZyWALL<br>Série ATP ZyWALL<br>Série ZYWALL VPN | ZLD v4.32+ | (não testado) | [VTI sobre IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP sobre IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) As versões 8.4+ do Cisco ASA adicionam suporte para IKEv2 e permitem ligar ao gateway de VPN do Azure através da política IPsec/IKE personalizada com a opção "UsePolicyBasedTrafficSelectors". Veja este [artigo de procedimentos](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\*\*) Os routers da Série ISR 7200 só suportam VPNs PolicyBased.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Descarregue scripts de configuração de dispositivos VPN do Azure

Para determinados dispositivos, pode descarregar scripts de configuração diretamente do Azure. Para obter mais informações e descarregar instruções, consulte [scripts de configuração do dispositivo VPN de descarregamento](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Dispositivos com scripts de configuração disponíveis

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>Dispositivos VPN não validados

Se não vir o seu dispositivo listado na tabela de Dispositivos VPN Validados, o seu dispositivo poderá, mesmo assim, funcionar com uma ligação Site a Site. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>Editar os exemplos de configuração do dispositivo

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

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>Parâmetros IPsec/IKE predefinidos

As tabelas abaixo contêm as combinações de algoritmos e parâmetros que os gateways Azure VPN usam na configuração predefinida **(políticas predefinidas).** Para gateways de VPN baseados em rota criados com o modelo de implementação da Gestão de Recursos do Azure, pode especificar uma política personalizada em cada ligação individual. Veja o artigo [Configurar a política IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções detalhadas.

Além disso, deve fixar O **MSS** TCP a **1350**. Se os dispositivos VPN não suportarem a afixação MSS, pode, em alternativa, definir o **MTU** na interface de túnel para **1400** bytes.

Nas seguintes tabelas:

* SA = Associação de Segurança
* IKE Fase 1 também é denominado "Modo Principal"
* IKE Fase 2 também é denominado "Modo Rápido"

### <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros de IKE Fase 1 (Modo Principal)

| **Propriedade**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Versão do IKE           |IKEv1              |IKEv1 e IKEv2    |
| Grupo Diffie-Hellman  |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-partilhada     |Chave Pré-partilhada     |
| Algoritmos de Encriptação e Hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Duração de SA           |28 800 segundos     |28 800 segundos     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parâmetros de IKE Fase 2 (Modo Rápido)

| **Propriedade**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Versão do IKE                   |IKEv1          |IKEv1 e IKEv2                              |
| Algoritmos de Encriptação e Hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Ofertas de RouteBased QM SA](#RouteBasedOffers) |
| Duração de SA (Tempo)            |3600 segundos  |27,000 segundos                               |
| Duração de SA (Bytes)           |102 400 000 KB |102 400 000 KB                               |
| Perfect Forward Secrecy (PFS) |No             |[Ofertas de RouteBased QM SA](#RouteBasedOffers) |
| Deteção de Elemento Inutilizado (DPD)     |Não suportado  |Suportado                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>Ofertas de RouteBased VPN IPsec Security Association (SA do Modo Rápido de IKE)

A tabela seguinte apresenta as Ofertas de SA IPsec (Modo Rápido de IKE). As ofertas estão listadas pela ordem de preferência com que a oferta é apresentada ou aceite.

#### <a name="azure-gateway-as-initiator"></a>Gateway do Azure como iniciador

|-  |**Encriptação**|**Autenticação**|**Grupo PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhum         |
| 2 |AES256        |SHA1              |Nenhum         |
| 3 |3DES          |SHA1              |Nenhum         |
| 4 |AES256        |SHA256            |Nenhum         |
| 5 |AES128        |SHA1              |Nenhum         |
| 6 |3DES          |SHA256            |Nenhum         |

#### <a name="azure-gateway-as-responder"></a>Gateway do Azure como dispositivo de resposta

|-  |**Encriptação**|**Autenticação**|**Grupo PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhum         |
| 2 |AES256        |SHA1              |Nenhum         |
| 3 |3DES          |SHA1              |Nenhum         |
| 4 |AES256        |SHA256            |Nenhum         |
| 5 |AES128        |SHA1              |Nenhum         |
| 6 |3DES          |SHA256            |Nenhum         |
| 7 |DES           |SHA1              |Nenhum         |
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
| 22|AES128        |SHA256            |Nenhum         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Pode especificar a encriptação IPsec ESP NULL com gateways de VPN RouteBased e HighPerformance. A encriptação baseada em Nulo não proporciona proteção aos dados em trânsito e só deve ser utilizada quando é preciso ter débito máximo e latência mínima. Os clientes podem optar por utilizar esta opção em cenários de comunicação de VNet a VNet ou quando a encriptação está a ser aplicada noutro ponto da solução.
* Para a conectividade em vários locais através da Internet, utilize as predefinições do gateway de VPN do Azure com a encriptação e os algoritmos hash listados nas tabelas acima, para garantir a segurança da sua comunicação crítica.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>Problemas de compatibilidade de dispositivos conhecidos

> [!IMPORTANT]
> Estes são os problemas de compatibilidade conhecidos entre dispositivos VPN de terceiros e gateways de VPN do Azure. A equipa do Azure está a trabalhar ativamente com os fornecedores para resolver os problemas aqui listados. Assim que os problemas são resolvidos, esta página será atualizada com as informações mais atualizadas. Volte a verificar periodicamente.
>
>

### <a name="feb-16-2017"></a>16 de fevereiro de 2017

**Dispositivos da Palo Alto Networks com versão anterior à 7.1.4** para VPN baseado em rota do Azure: se estiver a utilizar dispositivos da Palo Alto Networks com versão do SO PAN anterior à 7.1.4 e ocorrerem problemas de conectividade para gateways de VPN baseados em rota do Azure, execute os seguintes passos:

1. Verifique a versão de firmware do seu dispositivo da Palo Alto Networks. Se a sua versão do SO PAN for anterior à 7.1.4, atualize para 7.1.4.
2. No dispositivo da Palo Alto Networks, altere a duração da Fase 2 SA (ou SA de Modo Rápido) para 28,800 segundos (8 horas) quando ligar ao gateway de VPN do Azure.
3. Se ainda ocorrerem problemas de conectividade, abra um pedido de suporte a partir do portal do Azure.

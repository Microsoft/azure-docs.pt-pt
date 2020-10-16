---
title: Azure VMware Solution by CloudSimple - Estique uma rede de camadas 2 no local para a Nuvem Privada
description: Descreve como configurar uma VPN layer 2 entre NSX-T numa CloudSimple Private Cloud e um cliente NSX Edge autónomo no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a530a6f656f37657a198af85d93d5404ac88d0e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83651021"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrar cargas de trabalho com redes ampliadas de Camada 2

Neste guia, você aprenderá a usar a Camada 2 VPN (L2VPN) para esticar uma rede Layer 2 do seu ambiente no local até à cloudSimple Private Cloud. Esta solução permite a migração de cargas de trabalho em execução no ambiente VMware no local para a Nuvem Privada em Azure dentro do mesmo espaço de endereço da sub-rede sem ter de re-IP as suas cargas de trabalho.

O alongamento baseado em L2VPN das redes Layer 2 pode funcionar com ou sem redes baseadas em NSX no seu ambiente VMware no local. Se não tiver redes baseadas em NSX para cargas de trabalho no local, pode utilizar um Gateway de Serviços NSX Edge autónomo.

> [!NOTE]
> Este guia cobre o cenário em que os centros de dados da Cloud Privada estão ligados sobre a VPN site-to-site.

## <a name="deployment-scenario"></a>Cenário de implementação

Para esticar a sua rede no local utilizando l2VPN, tem de configurar um servidor L2VPN (router NSX-T Tier0 de destino) e um cliente L2VPN (cliente autónomo de origem).  

Neste cenário de implantação, a sua Nuvem Privada está ligada ao seu ambiente no local através de um túnel VPN site-to-site que permite a gestão no local e sub-redes vMotion para comunicar com a gestão da Nuvem Privada e sub-redes vMotion. Este arranjo é necessário para cross vCenter vMotion (xVC-vMotion). Um router NSX-T Tier0 é implantado como um servidor L2VPN na Nuvem Privada.

O NSX Edge autónomo é implantado no seu ambiente no local como cliente L2VPN e posteriormente emparelhado com o servidor L2VPN. Um ponto final do túnel GRE é criado em cada lado e configurado para 'esticar' a rede de camadas 2 no local para a sua Nuvem Privada. Esta configuração é representada na seguinte figura.

![Cenário de implementação](media/l2vpn-deployment-scenario.png)

Para saber mais sobre a migração utilizando l2 VPN, consulte [redes privadas virtuais](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Pré-requisitos para a implementação da solução

Verifique se o seguinte está em vigor antes de implementar e configurar a solução:

* A versão no local vSphere é de 6.7U1+ ou 6.5P03+.
* A licença vSphere no local está no nível Enterprise Plus (para vSphere Distributed Switch).
* Identifique a rede de carga de trabalho Camada 2 a ser esticada para a sua Nuvem Privada.
* Identifique uma rede Layer 2 no seu ambiente no local para implantar o seu aparelho cliente L2VPN.
* [Uma Nuvem Privada já está criada.](create-private-cloud.md)
* A versão do aparelho NSX-T Edge autónomo é compatível com a versão NSX-T Manager (NSX-T 2.3.0) utilizada no seu ambiente Private Cloud.
* Foi criado um grupo porta-tronco no vCenter no local com transmissões forjadas ativadas.
* Um endereço IP público foi reservado para o endereço IP de uplink do cliente autónomo NSX-T, e 1:1 NAT está em vigor para tradução entre os dois endereços.
* O reencaminhamento de DNS está definido nos servidores DNS no local para o domínio az.cloudsimple.io para apontar para os servidores DNS da Cloud Privada.
* A latência rtt é inferior ou igual a 150 ms, conforme exigido para vMotion trabalhar em todos os dois locais.

## <a name="limitations-and-considerations"></a>Limitações e considerações

As seguintes listas de tabelas suportadas versões vSphere e tipos de adaptador de rede.  

| versão vSphere | Fonte vSwitch tipo | Controlador virtual NIC | Tipo alvo vSwitch | Suportada? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Todos | DVS | Todos | DVS | Sim |
| vSphere 6.7UI ou superior, 6.5P03 ou superior | DVS | VMXNET3 | N-VDS | Sim |
| vSphere 6.7UI ou superior, 6.5P03 ou superior | DVS | E1000 | N-VDS | [Não suportado por VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI ou 6.5P03, NSX-V ou versões abaixo de NSX-T2.2, 6.5P03 ou superior | Todos | Todos | N-VDS | [Não suportado por VWware](https://kb.vmware.com/s/article/56991) |

A partir da versão VMware NSX-T 2.3:

* O interruptor lógico no lado da Nuvem Privada que é esticado para as instalações sobre a L2VPN não pode ser encaminhado ao mesmo tempo. O interruptor lógico esticado não pode ser ligado a um router lógico.
* L2VPN e VPNs IPSEC baseados em rotas só podem ser configurados usando chamadas API.

Para mais informações, consulte [Redes Privadas Virtuais](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Endereço de implantação L2 VPN da amostra

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rede no local onde o ESG autónomo (cliente L2 VPN) é implantado

| **Item** | **Valor** |
|------------|-----------------|
| Nome da rede | MGMT_NET_VLAN469 |
| Rio VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Endereço IP do aparelho de borda autónoma | 10.250.0.111 |
| Endereço IP do aparelho de borda autónoma | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rede de instalações a esticada

| **Item** | **Valor** |
|------------|-----------------|
| Rio VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Esquema IP de nuvem privada para NSX-T Tier0 Router (L2 VPN servir)

| **Item** | **Valor** |
|------------|-----------------|
| Interface loopback | 192.168.254.254/32 |
| Interface de túnel | 5.5.5.1/29 |
| Interruptor lógico (esticado) | Stretch_LS |
| Interface loopback (endereço IP NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rede Private Cloud a ser mapeada para a rede estendida

| **Item** | **Valor** |
|------------|-----------------|
| Rio VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Pegue o ID do router lógico necessário para l2VPN

Os passos seguintes mostram como obter o ID do router lógico de Tier0 DR para os serviços IPsec e L2VPN. O ID do router lógico é necessário mais tarde na implementação do L2VPN.

1. Inscreva-se no NSX-T Manager `https://*nsx-t-manager-ip-address*` e selecione **Networking**  >  **Routers**  >  **Provider-LR**  >  **Overview**. Para **o modo de alta disponibilidade**, selecione **Active-Standby**. Esta ação abre uma janela pop-up que mostra o Edge VM no qual o router Tier0 está atualmente ativo.

    ![Selecione active-standby](media/l2vpn-fetch01.png)

2. Selecione **Fabric**  >  **Bordas de Nós**  >  **de Tecidos**. Tome nota do endereço IP de gestão do VM de borda ativo (Edge VM1) identificado no passo anterior.

    ![IP de gestão de notas](media/l2vpn-fetch02.png)

3. Abra uma sessão de SSH para o endereço IP de gestão do Edge VM. Executar o comando com o ```get logical-router``` nome **de** utilizador e palavra-passe **CloudSimple 123!**

    ![obter saída de router lógica](media/l2vpn-fetch03.png)

4. Se não vir uma entrada 'DR-Provider-LR', complete os seguintes passos.

5. Crie dois interruptores lógicos apoiados por sobreposição. Um interruptor lógico é esticado para as instalações onde residem as cargas de trabalho migradas. Outro interruptor lógico é um interruptor falso. Para obter instruções, consulte [Criar uma Switch lógica](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) na documentação VMware.

    ![Criar interruptor lógico](media/l2vpn-fetch04.png)

6. Fixe o interruptor manequim ao router Tier1 com um endereço IP local de ligação ou qualquer sub-rede não sobreposta a partir do local ou da sua Nuvem Privada. Consulte [uma porta Downlink num Router Lógico tier-1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) na documentação VMware.

    ![Ligue o interruptor falso](media/l2vpn-fetch05.png)

7. Volte a executar o `get logical-router` comando na sessão SSH do Edge VM. É apresentado o UUID do router lógico "DR-Provider-LR". Tome nota do UUID, que é necessário ao configurar o L2VPN.

    ![obter saída de router lógica](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Pegue o ID de comutador lógico necessário para l2VPN

1. Inscreva-se no NSX-T Manager `https://nsx-t-manager-ip-address` ().
2. Selecione **Networking**  >  interruptores de**comutação**de rede  >  **Switches**  >  **<\Visão \> **geral do interruptor  >  **lógico**.
3. Tome nota do UUID do interruptor lógico de alongamento, que é necessário ao configurar o L2VPN.

    ![obter saída de router lógica](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerações de encaminhamento e segurança para a L2VPN

Para estabelecer uma VPN baseada em rotas IPsec entre o router NSX-T Tier0 e o cliente autónomo NSX Edge, a interface loopback do router NSX-T Tier0 deve ser capaz de comunicar com o endereço IP público do cliente autónomo NSX no local sobre o UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Permitir UDP 500/4500 para IPsec

1. [Crie um endereço IP público](public-ips.md) para a interface loopback NSX-T Tier0 no portal CloudSimple.

2. [Crie uma tabela de firewall](firewall.md) com regras imponentes que permitam o tráfego de entrada UDP 500/ 4500 e fixe a tabela de firewall à sub-rede NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anuncie a interface loopback IP para a rede de base

1. Crie uma rota nula para a rede de interface loopback. Inscreva-se no NSX-T Manager e selecione rotas de **encaminhamento**de  >  **encaminhamento**de rede  >  **Routers**  >  **Fornecedor-LR**Rotas  >  **Routing**  >  **Estáticas**de encaminhamento . Clique em **Adicionar**. Para **a Rede**, insira o endereço IP da interface de backback. Para **Next Hops**, clique em **Adicionar**, especifique 'Nulo' para o próximo salto e mantenha o padrão de 1 para Admin Distance.

    ![Adicionar rota estática](media/l2vpn-routing-security01.png)

2. Crie uma lista de prefixos IP. Inscreva-se no NSX-T **Networking**Manager e selecione  >  **Routing**  >  **listas**  >  de prefixos IP de encaminhamento de encaminhamento de rede de encaminhamento de rotas de**encaminhamento.**  >  **Routing**  >  **IP Prefix Lists** Clique em **Adicionar**. Insira um nome para identificar a lista. Para **Prefixos,** clique em **Adicionar** duas vezes. Na primeira linha, insira '0.0.0.0/0' para **rede** e 'Deny' para **ação**. Na segunda linha, **selecione Any** for **Network** and **Permit** for **Action**.
3. Anexe a lista de prefixos IP a ambos os vizinhos BGP (TOR). A anexação da lista de prefixos IP ao vizinho BGP impede que a rota predefinida seja publicitada em BGP aos interruptores TOR. No entanto, qualquer outra rota que inclua a rota nula irá anunciar o endereço IP da interface loopback para os interruptores TOR.

    ![Criar lista de prefixos IP](media/l2vpn-routing-security02.png)

4. Inscreva-se no NSX-T **Networking**Manager e selecione  >  Routers de**encaminhamento**de rede  >  **Routers**  >  **Fornecedor-LR**  >  **Encaminhamento**  >  **BGP**  >  **BGP Neighbors**. Selecione o primeiro vizinho. Clique **em**  >  **Editar Famílias de Endereços.** Para a família IPv4, edite a coluna **'Filtro Out'** e selecione a lista de prefixos IP que criou. Clique em **Guardar**. Repita este passo para o segundo vizinho.

    ![Anexar lista de prefixos IP 1 ](media/l2vpn-routing-security03.png) ![ Anexar lista de prefixos IP 2](media/l2vpn-routing-security04.png)

5. Redistribua a rota estática nula em BGP. Para anunciar a rota da interface loopback para a base, você deve redistribuir a rota estática nula em BGP. Inscreva-se no NSX-T **Networking**Manager e selecione  >  Routers de**encaminhamento**de rede  >  **Routers**  >  **Fornecedor-LR**  >  **Routeing**  >  **Routes Redistribution**  >  **Neighbors**. Selecione **Provider-LR-Route_Redistribution** e clique em **Editar**. Selecione a caixa de verificação **estática** e clique **em Guardar**.

    ![Redistribuir rota estática nulo em BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configure uma VPN baseada em rotas no router NSX-T Tier0

Utilize o seguinte modelo para preencher todos os detalhes para configurar uma VPN baseada em rotas no router NSX-T Tier0. Os UUIDs em cada chamada POST são necessários em chamadas POST subsequentes. Os endereços IP para as interfaces de loopback e túnel para L2VPN devem ser únicos e não se sobrepor com as redes no local ou private Cloud.

Os endereços IP escolhidos para interface de loopback e túnel utilizados para L2VPN devem ser únicos e não se sobrepor com as redes de nuvem privada ou no local. A rede de interface loopback deve ser sempre /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Para todas as chamadas API seguintes, substitua o endereço IP pelo seu endereço IP do NSX-T Manager. Pode executar todas estas chamadas API do cliente POSTMAN ou utilizando `curl` comandos.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Ativar o serviço IPSec VPN no router lógico

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Criar perfis: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Criar perfis: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Criar perfis: Túnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Criar um ponto final local

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Criar um ponto final de pares

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Criar uma sessão VPN baseada em rotas

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configure L2VPN no router NSX-T Tier0

Preencha as seguintes informações após cada chamada DOM. Os IDs são necessários em chamadas POST subsequentes.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Criar o serviço L2VPN

A saída do seguinte comando GET estará em branco, porque a configuração ainda não está completa.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Para o seguinte comando POST, o ID do router lógico é o UUID do router lógico Tier0 DR obtido anteriormente.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Criar a sessão L2VPN

Para o seguinte comando POST, o ID de serviço L2VPN é o ID que acabou de obter e o IPsec VPN sessão ID é o ID obtido na secção anterior.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Estas chamadas criam um ponto final do túnel GRE. Para verificar o estado, verifique o seguinte comando.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Criar porta lógica com o ID do túnel especificado

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Obtenha o código de pares para L2VPN no lado NSX-T

Obtenha o código de pares do ponto final NSX-T. O código de pares é necessário ao configurar o ponto final remoto. O L2VPN <> de sessão pode ser obtido a partir da secção anterior. Para mais informações, consulte o [Guia API NSX-T 2.3.](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Implementar o cliente autónomo NSX-T (no local)

Antes de implementar, verifique se as suas regras de firewall no local permitem o tráfego uDP 500/4500 de/para o endereço IP público CloudSimple que foi reservado anteriormente para a interface de loopback do router NSX-T T0. 

1. [Descarregue o Cliente NSX Edge autónomo](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF e Extrair os ficheiros do pacote descarregado numa pasta.

    ![Baixar cliente autónomo NSX Edge](media/l2vpn-deploy-client01.png)

2. Vá à pasta com todos os ficheiros extraídos. Selecione todos os vmdks (NSX-l2t-client-large.mf e NSX-l2t-client-large.ovf para tamanho de aparelho grande ou NSX-l2t-client-Xlarge.mf e NSX-l2t-client-xlarge.ovf para tamanho extra grande do aparelho). Clique em **Seguinte**.

    ![Selecione ](media/l2vpn-deploy-client02.png) ![ modelo De selecionar modelo](media/l2vpn-deploy-client03.png)

3. Insira um nome para o cliente autónomo NSX-T e clique em **Seguinte**.

    ![Insira o nome do modelo](media/l2vpn-deploy-client04.png)

4. Clique em **seguida,** conforme necessário para chegar às definições da datastore. Selecione a loja de dados apropriada para cliente autónomo NSX-T e clique em **Seguinte**.

    ![Selecione datastore](media/l2vpn-deploy-client06.png)

5. Selecione os grupos portais corretos para o Tronco (Tronco PG), Interface Pública (Uplink PG) e HA (Uplink PG) para o cliente autónomo NSX-T. Clique em **Seguinte**.

    ![Selecione grupos portuários](media/l2vpn-deploy-client07.png)

6. Preencha os seguintes detalhes no ecrã **do modelo Personalizar** e clique em **Seguinte**:

    Expandir L2T:

    * **Endereço de pares**. Introduza o endereço IP reservado no portal Azure CloudSimple para a interface NSX-T Tier0 Loopback.
    * **Código de Pares**. Cole o código de pares obtido a partir do último passo da implementação do Servidor L2VPN.
    * **Sub Interfaces VLAN (ID do túnel)**. Introduza o ID VLAN para ser esticado. Em parênteses (), entre no iD do túnel que foi previamente configurado.

    Expandir interface de uplink:

    * **Endereço IP DNS**. Insira o endereço DNS IP no local.
    * **Gateway predefinido**.  Introduza a porta de entrada padrão do VLAN que funcionará como uma porta de entrada padrão para este cliente.
    * **Endereço IP**. Introduza o endereço IP uplink do cliente autónomo.
    * **Comprimento do prefixo**. Introduza o comprimento do prefixo da sub-rede VLAN/sub-rede de ligação.
    * **CLI admin/enable/root User Password**. Desa estada a palavra-passe para administração /ativar /conta raiz.

      ![Personalizar modelo ](media/l2vpn-deploy-client08.png)
       ![ Personalizar modelo - mais](media/l2vpn-deploy-client09.png)

7. Reveja as definições e clique **em Terminar**.

    ![Configuração completa](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configure um porto de afundamento no local

Se um dos sites VPN não tiver O NSX implantado, pode configurar uma VPN L2 implantando um NSX Edge autónomo nesse site. Um NSX Edge autónomo é implantado usando um ficheiro OVF num hospedeiro que não é gerido por NSX. Isto implementa um aparelho NSX Edge Services Gateway para funcionar como um cliente L2 VPN.

Se um porta-bagagens autónomo vNIC estiver ligado a um interruptor distribuído vSphere, é necessário um modo promíscuo ou uma porta de pia para a função L2 VPN. A utilização do modo promíscuo pode causar pings duplicados e respostas duplicadas. Por esta razão, utilize o modo porta da pia na configuração NSX Edge autónoma L2 VPN. Consulte a [porta de cedimento](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) de uma pia na documentação VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verificação IPsec VPN e L2VPN

Utilize os seguintes comandos para verificar as sessões IPsec e L2VPN a partir de NSX-T Edge autónomo.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Utilize os seguintes comandos para verificar as sessões IPsec e L2VPN do router NSX-T Tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Utilize os seguintes comandos para verificar a porta da pia no hospedeiro ESXi, onde reside o VM do cliente autónomo NSX-T no ambiente no local.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```

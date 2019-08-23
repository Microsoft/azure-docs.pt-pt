---
title: Solução do Azure VMware por CloudSimple-alongar uma rede de camada 2 local para nuvem privada
description: Descreve como configurar uma VPN de camada 2 entre o NSX-T em uma nuvem privada do CloudSimple e um cliente autônomo do NSX Edge local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 55401ca498f06aa0b959c3926f2a07f40e7fb638
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972623"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrar cargas de trabalho usando redes ampliadas de camada 2

Neste guia, você aprenderá a usar a VPN de camada 2 (L2VPN) para ampliar uma rede de camada 2 do seu ambiente local para sua nuvem privada do CloudSimple. Essa solução permite a migração de cargas de trabalho em execução em seu ambiente VMware local para a nuvem privada no Azure no mesmo espaço de endereço de sub-rede sem a necessidade de reutilizar o IP de suas cargas de trabalho.

O alargamento baseado em L2VPN das redes de camada 2 pode trabalhar com ou sem redes baseadas no NSX em seu ambiente VMware local. Se você não tiver redes baseadas no NSX para cargas de trabalho locais, poderá usar um gateway de serviços de borda autônomo do NSX.

> [!NOTE]
> Este guia aborda o cenário em que o local e os data centers de nuvem privada estão conectados via VPN site a site.

## <a name="deployment-scenario"></a>Cenário de implantação

Para ampliar sua rede local usando o L2VPN, você deve configurar um servidor L2VPN (destino NSX-T tier0 router) e um cliente L2VPN (cliente autônomo de origem).  

Nesse cenário de implantação, sua nuvem privada está conectada ao seu ambiente local por meio de um túnel VPN site a site que permite o gerenciamento local e as sub-redes do vMotion para se comunicarem com as sub-redes do vMotion e do gerenciamento de nuvem privada. Essa organização é necessária para o vCenter vMotion (xVC-vMotion) cruzado. Um roteador do NSX-T tier0 é implantado como um servidor L2VPN na nuvem privada.

O NSX Edge autônomo é implantado em seu ambiente local como um cliente L2VPN e, em seguida, emparelhado com o servidor L2VPN. Um ponto de extremidade de túnel GRE é criado em cada lado e configurado para "ampliar" a rede de camada 2 local para sua nuvem privada. Essa configuração é descrita na figura a seguir.

![Cenário de implantação](media/l2vpn-deployment-scenario.png)

Para saber mais sobre a migração usando VPN L2, confira [redes virtuais privadas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Pré-requisitos para implantar a solução

Verifique se os itens a seguir estão em vigor antes de implantar e configurar a solução:

* A versão do vSphere local é 6.7 U1 + ou 6.5 P03 +.
* A licença local do vSphere está no nível Enterprise Plus (para vSphere Distributed Switch).
* Identifique a rede da camada de carga de trabalho 2 a ser ampliada para sua nuvem privada.
* Identifique uma rede de camada 2 em seu ambiente local para implantar seu dispositivo cliente do L2VPN.
* [Uma nuvem privada já foi criada](create-private-cloud.md).
* A versão do dispositivo independente NSX-T Edge é compatível com a versão do Gerenciador de NSX-T (NSX-T 2.3.0) usada em seu ambiente de nuvem privada.
* Um grupo de portas de tronco foi criado no vCenter local com transmissões forjadas habilitadas.
* Um endereço IP público foi reservado para uso para o endereço IP de uplink do cliente autônomo do NSX-T e 1:1 NAT está em vigor para tradução entre os dois endereços.
* O encaminhamento de DNS é definido nos servidores DNS locais para o domínio az.cloudsimple.io para apontar para os servidores DNS da nuvem privada.
* A latência de RTT é menor ou igual a 150 ms, conforme necessário para que o vMotion funcione nos dois sites.

## <a name="limitations-and-considerations"></a>Limitações e considerações

A tabela a seguir lista as versões vSphere e os tipos de adaptadores de rede com suporte.  

| versão do vSphere | Tipo de vSwitch de origem | Driver NIC virtual | Tipo de vSwitch de destino | Suportada? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Todos | DVS | Todos | DVS | Sim |
| interface do usuário do vSphere 6.7 ou superior, 6.5 P03 ou superior | DVS | VMXNET3 | N-VDS | Sim |
| interface do usuário do vSphere 6.7 ou superior, 6.5 P03 ou superior | DVS | E1000 | N-VDS | [Sem suporte por VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI ou 6.5 P03, NSX-V ou versões abaixo do NSX-T 2.2, 6.5 P03 ou superior | Todos | Todos | N-VDS | [Sem suporte por VWware](https://kb.vmware.com/s/article/56991) |

A partir da versão do VMware NSX-T 2,3:

* O comutador lógico no lado da nuvem privada que é ampliado para o local sobre L2VPN não pode ser roteado ao mesmo tempo. O comutador lógico ampliado não pode ser conectado a um roteador lógico.
* As VPNs IPSEC baseadas em rota e L2VPN só podem ser configuradas usando chamadas à API.

Para obter mais informações, consulte [redes virtuais privadas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) na documentação do VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Endereçamento de implantação de VPN L2 de exemplo

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Rede local em que o ESG autônomo (cliente VPN L2) está implantado

| **Item** | **Valor** |
|------------|-----------------|
| Nome da rede | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Endereço IP do dispositivo de borda autônomo | 10.250.0.111 |
| Endereço IP do NAT do dispositivo de borda autônomo | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Rede local a ser ampliada

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Esquema de IP de nuvem privada para o roteador do tier0 do NSX – T (L2 VPN servindo)

| **Item** | **Valor** |
|------------|-----------------|
| Interface de loopback | 192.168.254.254/32 |
| Interface de túnel | 5.5.5.1/29 |
| Comutador lógico (ampliado) | Stretch_LS |
| Interface de loopback (endereço IP de NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Rede de nuvem privada a ser mapeada para a rede ampliada

| **Item** | **Valor** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Buscar a ID de roteador lógico necessária para L2VPN

As etapas a seguir mostram como buscar a ID do roteador lógico da instância do roteador lógico tier0 DR para os serviços IPsec e L2VPN. A ID do roteador lógico é necessária posteriormente ao implementar o L2VPN.

1. Entre no NSX-T Manager (e https://nsx-t-manager-ip-address) selecione**provedor de** > **roteadores** >  de **rede** >  –**visão geral**do LR. Para o **modo de alta disponibilidade**, selecione **ativo-em espera**. Essa ação abre uma janela pop-up que mostra a VM de borda na qual o roteador tier0 está ativo no momento.

    ![Selecionar ativo-em espera](media/l2vpn-fetch01.png)

2. Selecione **malha** > nósbordas > . Anote o endereço IP de gerenciamento da VM de borda ativa (VM1 de borda) identificada na etapa anterior.

    ![IP de gerenciamento de observação](media/l2vpn-fetch02.png)

3. Abra uma sessão SSH para o endereço IP de gerenciamento da VM de borda. Execute o ```get logical-router``` comando com nome de usuário **admin** e senha **CloudSimple 123!** .

    ![obter saída de roteador lógico](media/l2vpn-fetch03.png)

4. Se você não vir uma entrada ' DR-Provider-LR ', conclua as etapas a seguir.

5. Crie dois comutadores lógicos com backup de sobreposição. Um comutador lógico é ampliado para o local onde as cargas de trabalho migradas residem. Outro comutador lógico é um comutador fictício. Para obter instruções, consulte [criar um comutador lógico](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) na documentação do VMware.

    ![Criar comutador lógico](media/l2vpn-fetch04.png)

6. Anexe o comutador fictício ao roteador nível 1 com um endereço IP local de link ou qualquer sub-rede não sobreposta do local ou da sua nuvem privada. Consulte [Adicionar uma porta de downlink em um roteador lógico de camada 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) na documentação do VMware.

    ![Anexar comutador fictício](media/l2vpn-fetch05.png)

7. Execute o `get logical-router` comando novamente na sessão SSH da VM de borda. O UUID do roteador lógico ' DR-Provider-LR ' é exibido. Anote o UUID, que é necessário ao configurar o L2VPN.

    ![obter saída de roteador lógico](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Buscar a ID de comutador lógico necessária para L2VPN

1. Entre no [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Selecione comutadores de**troca** > de **rede** > > * * < opção \Logical > \ * * > **visão geral**.
3. Anote o UUID do comutador lógico de ampliação, que é necessário ao configurar o L2VPN.

    ![obter saída de roteador lógico](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Considerações de roteamento e segurança para L2VPN

Para estabelecer uma VPN baseada em rota IPsec entre o roteador NSX-T tier0 e o cliente autônomo do NSX Edge, a interface de loopback do roteador NSX-T tier0 deve ser capaz de se comunicar com o endereço IP público do cliente do NSX autônomo local por UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Permitir UDP 500/4500 para IPsec

1. [Crie um endereço IP público](public-ips.md) para a interface de loopback do NSX-T tier0 no portal do CloudSimple.

2. [Crie uma tabela de firewall](firewall.md) com regras com estado que permitam o tráfego de entrada UDP 500/4500 e anexe a tabela de firewall à sub-rede NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anunciar o IP da interface de loopback para a rede underlay

1. Crie uma rota nula para a rede de interface de loopback. Entre no NSX-T Manager e selecione **rede** > **Roteamento** > **roteadores** > **provedor-LR** > **rotas estáticas**de**Roteamento** > . Clique em **Adicionar**. Para **rede**, insira o endereço IP da interface de loopback. Para os **próximos saltos**, clique em **Adicionar**, especifique ' nulo ' para o próximo salto e mantenha o padrão 1 para distância do administrador.

    ![Adicionar rota estática](media/l2vpn-routing-security01.png)

2. Crie uma lista de prefixos IP. Entre no NSX-T Manager e selecione **rede** > **Roteamento** > **roteadores** > **provedor-LR** > **listas de prefixo de IP**de**Roteamento** > . Clique em **Adicionar**. Insira um nome para identificar a lista. Paraprefixos, clique em **Adicionar** duas vezes. Na primeira linha, digite "0.0.0.0/0" para **rede** e "negar" para **ação**. Na segunda linha, selecione **qualquer** para **rede** e **permissão** para **ação**.
3. Anexe a lista de prefixos IP a ambos os vizinhos de BGP (TOR). Anexar a lista de prefixo de IP ao vizinho BGP impede que a rota padrão seja anunciada no BGP para os comutadores TOR. No entanto, qualquer outra rota que inclua a rota nula anunciará o endereço IP da interface de loopback para os comutadores TOR.

    ![Criar lista de prefixo IP](media/l2vpn-routing-security02.png)

4. Entre no NSX-T Manager e selecione **rede** > **Roteamento** > **roteadores** > **provedor-LR** > **Roteamento** > **BGP**  >  **Vizinhos**. Selecione o primeiro vizinho. Clique em **Editar** > **famílias de endereços**. Para a família IPv4, edite a coluna **Filtro out** e selecione a lista de prefixo IP que você criou. Clique em **Guardar**. Repita essa etapa para o segundo vizinho.

    ![Anexar lista de prefixo IP](media/l2vpn-routing-security03.png) 1 ![anexar lista de prefixo IP 2](media/l2vpn-routing-security04.png)

5. Redistribua a rota estática nula para BGP. Para anunciar a rota de interface de loopback para o underlay, você deve redistribuir a rota estática nula para o BGP. Entre no NSX-T Manager e selecione **rede** > **Roteamento** > **roteadores** > **provedor-LR** > **Roteamento** > **rota redistribuição**  >  **Vizinhos**. Selecione **Provider-LR-Route_Redistribution** e clique em **Editar**. Marque a caixa de seleção **estático** e clique em **salvar**.

    ![Redistribuir a rota estática nula para BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Configurar uma VPN baseada em rota no roteador do tier0 do NSX-T

Use o modelo a seguir para preencher todos os detalhes de configuração de uma VPN baseada em rota no roteador NSX-T tier0. Os UUIDs em cada chamada POST são necessários nas chamadas POST subsequentes. Os endereços IP para as interfaces de loopback e de túnel para L2VPN devem ser exclusivos e não se sobrepõem às redes locais ou de nuvem privada.

Os endereços IP escolhidos para loopback e interface de túnel usados para L2VPN devem ser exclusivos e não se sobrepõem às redes locais ou de nuvem privada. A rede da interface de loopback sempre deve ser/32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
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

Para todas as chamadas de API a seguir, substitua o endereço IP pelo seu endereço IP do Gerenciador de NSX-T. Você pode executar todas essas chamadas de API do cliente do postmaster ou usando `curl` comandos.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Habilitar o serviço VPN IPSec no roteador lógico

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

### <a name="create-profiles-ike"></a>Criar perfis: PROTOCOLO

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

### <a name="create-profiles-tunnel"></a>Criar perfis: Caps

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

### <a name="create-a-local-endpoint"></a>Criar um ponto de extremidade local

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

### <a name="create-a-peer-endpoint"></a>Criar um ponto de extremidade de par

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

### <a name="create-a-route-based-vpn-session"></a>Criar uma sessão VPN baseada em rota

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

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Configurar L2VPN no roteador NSX-T tier0

Preencha as informações a seguir após cada chamada POST. As IDs são necessárias em chamadas POST subsequentes.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Criar o serviço L2VPN

A saída do comando GET a seguir ficará em branco, pois a configuração ainda não foi concluída.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Para o comando POST a seguir, a ID do roteador lógico é o UUID do roteador lógico tier0 DR obtido anteriormente.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Criar a sessão L2VPN

Para o comando POST a seguir, a ID do serviço L2VPN é a ID que você acabou de obter e a ID da sessão VPN IPsec é a ID obtida na seção anterior.

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

Essas chamadas criam um ponto de extremidade de túnel GRE. Para verificar o status, execute o comando a seguir.

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

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Criar porta lógica com a ID de túnel especificada

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

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Obter o código de par para L2VPN no lado do NSX-T

Obtenha o código de mesmo nível do ponto de extremidade do NSX-T. O código de mesmo nível é necessário ao configurar o ponto de extremidade remoto. O L2VPN < ID da sessão > pode ser obtido na seção anterior. Para obter mais informações, consulte o [Guia de API do NSX-T 2,3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Implantar o cliente independente do NSX-T (local)

Antes de implantar o, verifique se as regras de firewall local permitem o tráfego UDP 500/4500 de entrada e saída de/para o endereço IP público CloudSimple que foi reservado anteriormente para a interface de loopback do roteador do NSX-T T0. 

1. [Baixar o cliente autônomo do NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF e extraia os arquivos do grupo baixado para uma pasta.

    ![Baixar o cliente autônomo do NSX Edge](media/l2vpn-deploy-client01.png)

2. Vá para a pasta com todos os arquivos extraídos. Selecione todos os VMDKs (NSX-L2T-cliente-grande. MF e NSX-l2t-client-large. ovf para o tamanho do dispositivo grande ou NSX-L2T-Client-XLarge. MF e NSX-l2t-client-Xlarge. ovf para o tamanho do dispositivo extra de tamanho grande). Clique em **Seguinte**.

    ![](media/l2vpn-deploy-client02.png) Selecionar modelo selecionarmodelo![](media/l2vpn-deploy-client03.png)

3. Insira um nome para o cliente do NSX-T autônomo e clique em **Avançar**.

    ![Inserir nome do modelo](media/l2vpn-deploy-client04.png)

4. Clique em **Avançar** conforme necessário para acessar as configurações do repositório de armazenamento. Selecione o repositório de armazenamento apropriado para o cliente do NSX-T autônomo e clique em **Avançar**.

    ![Selecionar arquivo de dados](media/l2vpn-deploy-client06.png)

5. Selecione os grupos de portas corretos para o tronco (PG de tronco), público (uplink PG) e a interface de HA (uplink PG) para o cliente autônomo do NSX-T. Clique em **Seguinte**.

    ![Selecionar grupos de portas](media/l2vpn-deploy-client07.png)

6. Preencha os seguintes detalhes na tela **Personalizar modelo** e clique em **Avançar**:

    Expanda L2T:

    * **Endereço de mesmo nível**. Insira o endereço IP reservado no portal CloudSimple do Azure para a interface de loopback do NSX-T tier0.
    * **Código de mesmo nível**. Cole o código de mesmo nível obtido na última etapa da implantação do L2VPN Server.
    * **Sub interfaces VLAN (ID do túnel)** . Insira a ID da VLAN a ser ampliada. Entre parênteses (), insira a ID do túnel que foi previamente configurada.

    Expanda a interface de uplink:

    * **Endereço IP do DNS**. Insira o endereço IP do DNS local.
    * **Gateway padrão**.  Insira o gateway padrão da VLAN que atuará como um gateway padrão para esse cliente.
    * **Endereço IP**. Insira o endereço IP de uplink do cliente autônomo.
    * **Comprimento do prefixo**. Insira o comprimento do prefixo da VLAN/sub-rede de uplink.
    * **Administrador/habilitar/senha de usuário raiz da CLI**. Defina a senha para a conta do admin/Enable/root.

      ![Personalizar modelo](media/l2vpn-deploy-client08.png)
      de personalização de modelo![-mais](media/l2vpn-deploy-client09.png)

7. Examine as configurações e clique em **concluir**.

    ![Concluir configuração](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Configurar uma porta de coletor local

Se um dos sites VPN não tiver o NSX implantado, você poderá configurar uma VPN L2 implantando uma borda autônoma do NSX no site. Uma borda autônoma do NSX é implantada usando um arquivo OVF em um host que não é gerenciado pelo NSX. Isso implanta um dispositivo de gateway de serviços do NSX Edge para funcionar como um cliente VPN de L2.

Se um vNIC de tronco de borda autônomo estiver conectado a um comutador distribuído vSphere, o modo promíscuo ou uma porta de coletor será necessária para a função VPN L2. O uso do modo promíscuo pode causar pings duplicados e respostas duplicadas. Por esse motivo, use o modo de porta do coletor na configuração de borda de VPN autônoma do NSX do L2. Consulte a documentação [configurar um coletor](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) no VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Verificação de VPN e L2VPN IPsec

Use os comandos a seguir para verificar as sessões de IPsec e L2VPN da borda do NSX-T autônoma.

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

Use os comandos a seguir para verificar as sessões de IPsec e L2VPN do roteador de tier0 do NSX-T.

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

Use os comandos a seguir para verificar a porta do coletor no host ESXi em que a VM do cliente independente do NSX-T reside no ambiente local.

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

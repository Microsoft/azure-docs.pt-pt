---
title: Solução Azure VMware by CloudSimple - Configure a alta disponibilidade a partir de instalações para o gateway VPN CloudSimple
description: Descreve como configurar uma ligação de alta disponibilidade do seu ambiente no local para um gateway VPN CloudSimple habilitado para alta disponibilidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025270"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configure uma ligação de alta disponibilidade a partir de instalações para o gateway VPN CloudSimple

Os administradores de rede podem configurar uma ligação VPN site-para-site de alta disponibilidade do seu ambiente no local para um gateway VPN CloudSimple.

Este guia apresenta passos para configurar uma firewall no local para uma ligação vpn de alta disponibilidade do Site-para-Site IPsec. Os passos detalhados são específicos do tipo de firewall no local. Como exemplos, este guia apresenta passos para dois tipos de firewalls: Cisco ASA e Palo Alto Networks.

## <a name="before-you-begin"></a>Antes de começar

Complete as seguintes tarefas antes de configurar a firewall no local.

1. Verifique se a sua organização [foru os](create-nodes.md) nódosos necessários e criou pelo menos uma CloudSimple Private Cloud.
2. [Configure um portal VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre a sua rede no local e a sua CloudSimple Private Cloud.

Consulte a visão geral dos [gateways VPN](cloudsimple-vpn-gateways.md) para as propostas de fase 1 e fase 2 suportadas.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configure no local firewall Cisco ASA

As instruções nesta secção aplicam-se à versão 8.4 da Cisco ASA e posteriormente. No exemplo de configuração, a Versão 9.10 do software de segurança adaptável da Cisco adaptive é implementada e configurada no modo IKEv1.

Para que a VPN do Site-a-Site funcione, deve permitir que uDP 500/4500 e ESP (protocolo IP 50) do IP público primário e secundário (peer IP) da CloudSimple na interface externa do gateway Cisco ASA VPN no local.

### <a name="1-configure-phase-1-ikev1"></a>1. Configurar a fase 1 (IKEv1)

Para ativar a fase 1 (IKEv1) na interface exterior, introduza o seguinte comando CLI na firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Criar uma política IKEv1

Crie uma política IKEv1 que defina os algoritmos e métodos a utilizar para hashing, autenticação, grupo Diffie-Hellman, vida e encriptação.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Criar um grupo de túneis

Crie um grupo de túneis sob os atributos do IPsec. Configure o endereço IP dos pares e a chave pré-partilhada do túnel, que definiu ao [configurar o gateway VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Configurar a fase 2 (IPsec)

Para configurar a fase 2 (IPsec), crie uma lista de controlo de acesso (ACL) que defina o tráfego a ser encriptado e túnel. No exemplo seguinte, o tráfego de interesses provém do túnel que é proveniente da subnet local no local (10.16.1.0/24) até à subrede remota da Nuvem Privada (192.168.0.0/24). A ACL pode conter várias entradas se houver várias subredes entre os sites.

Nas versões Cisco ASA 8.4 e posteriormente, podem ser criados objetos ou grupos de objetos que servem de recipientes para as redes, subredes, endereços IP hospedeiros ou múltiplos objetos. Crie um objeto para o local e um objeto para as subredes remotas e use-os para as declarações cripto ACL e NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Defina uma subnet local no local como um objeto

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Defina a subnet remota CloudSimple como um objeto

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configure uma lista de acessos para o tráfego de interesses

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Configure o conjunto de transformação

Configure o conjunto de transformação (TS), que deve envolver a palavra-chave ```ikev1```. Os atributos de encriptação e hash especificados no TS devem coincidir com os parâmetros listados na [configuração Padrão para gateways VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configure o mapa cripto

Configure o mapa cripto, que contém estes componentes:

* Endereço IP peer
* ACL definida que contém o tráfego de interesses
* Conjunto de transformação

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Aplicar o mapa cripto

Aplique o mapa cripto na interface externa:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Confirmar as regras nat aplicáveis

Segue-se a regra NAT que é usada. Certifique-se de que o tráfego VPN não está sujeito a qualquer outra regra NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Amostra IPsec Site-to-Site VPN estabelecida saída da Cisco ASA

Saída da fase 1:

![Saída de fase 1 para firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Saída da fase 2:

![Saída de fase 2 para firewall Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configure no local firewall Palo Alto Networks

As instruções nesta secção aplicam-se à versão 7.1 da Palo Alto Networks e posteriormente. Neste exemplo de configuração, a Versão de Software série VM da Palo Alto 8.1.0 é implementada e configurada no modo IKEv1.

Para que a VPN do Site-a-Site funcione, deve permitir que uDP 500/4500 e ESP (protocolo IP 50) do IP público primário e secundário (PEER IP) da CloudSimple na interface externa do gateway Palo Alto Networks no local.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Criar interfaces de túneis primários e secundários

Inscreva-se na firewall de Palo Alto, selecione **Network** > **Interfaces** > **Tunnel** > **Add,** configure os seguintes campos e clique em **OK**.

* Nome da interface. O primeiro campo é autopovoado com palavra-chave "túnel". No campo adjacente, insira qualquer número entre 1 e 9999. Esta interface será usada como interface de túnel primário para transportar o tráfego Local-a-Local entre o centro de dados no local e a Nuvem Privada.
* Comentar, não é? Introduza comentários para facilitar a identificação do propósito do túnel
* Perfil de fluxo de rede. Mantenha a predefinição.
* O Config. Atribua interface para: Router virtual: Selecione **predefinido**. 
        Zona de Segurança: Selecione a zona para tráfego LAN fidedigno. Neste exemplo, o nome da zona para o tráfego LAN é "Trust".
* IPv4. Clique em **Adicionar** e adicione qualquer endereço ip não sobreposto no seu ambiente, que será atribuído à interface do túnel primário e será utilizado para monitorizar os túneis (explicado posteriormente).

Como esta configuração é para uma VPN de alta disponibilidade, são necessárias duas interfaces de túnel: uma primária e uma secundária. Repita os passos anteriores para criar a interface do túnel secundário. Selecione um id de túnel diferente e um endereço ip diferente não utilizado .32.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Configurar rotas estáticas para as subredes privadas cloud a serem alcançadas através do Site-to-Site VPN

As rotas são necessárias para que as subredes no local cheguem às subredes privadas CloudSimple.

Selecione **Rede** > **Virtual Routers** > *predefinidoS* > Rotas > **Estáticas****Adicionar,** configurar os seguintes campos e clicar **OK**.

* O nome. Insira qualquer nome para facilitar a identificação do propósito da rota.
* O destino. Especifique as subredes privadas cloudSimple a serem alcançadas através de interfaces de túnel S2S a partir do local
* A interface. Selecione a interface do túnel primário criada no passo-1 (Secção-2) a partir do dropdown. Neste exemplo, é o túnel 20.
* Próximo Hop. selecione **Nenhuma**.
* Distância de administrador. Mantenha a predefinição.
* A métrica. Insira qualquer valor de 1 a 65535. A chave é introduzir uma métrica mais baixa para a rota correspondente à interface do túnel primário em comparação com a rota correspondente interface do túnel secundário, tornando a rota preferida. Se o túnel.20 tem um valor métrico de 20 em oposição a um valor métrico de 30 para o túnel.30, o túnel .20 é preferido.
* Mesa de Rota. Mantenha a predefinição.
* Perfil de BFD. Mantenha a predefinição.
* Monitorização do caminho. deixe esta opção desselecionada.

Repita os passos anteriores para criar outra rota para as subredes Private Cloud usarem como uma rota secundária/de reserva através da interface de túnel secundário. Desta vez, selecione diferentes id do túnel e uma métrica mais alta do que para a rota primária.

### <a name="3-define-the-cryptographic-profile"></a>3. Definir o perfil criptográfico

Defina um perfil criptográfico que especifica os protocolos e algoritmos para identificação, autenticação e encriptação a utilizar para a instalação de túneis VPN na Fase 1 do IKEv1.

Selecione > Os perfis de rede**de expansão**da **rede** > **IKE Crypto** > **Add,** configure os seguintes campos e clique **em OK**.

* O nome. Introduza qualquer nome do perfil cripto-cripto ike.
* Grupo DH. Clique em **Adicionar** e selecione o grupo DH apropriado.
* Encriptação. Clique em **Adicionar** e selecione o método de encriptação apropriado.
* Autenticação. Clique em **Adicionar** e selecione o método de autenticação apropriado.
* A vida chave. Mantenha a predefinição.
* IKEv2 Autenticação Múltipla. Mantenha a predefinição.

### <a name="4-define-ike-gateways"></a>4. Definir gateways IKE

Defina as portas IKE para estabelecer a comunicação entre os pares em cada extremidade do túnel VPN.

Selecione > Os perfis de**rede de expansão**da **rede** > **IKE Gateways,** > **Add**configure os seguintes campos e clique em **OK**.

Separador geral:

* O nome. Introduza o nome para que o portal IKE seja espreitado com o par de VPN CloudSimple primário.
* Versão. Selecione **o modo apenas IKEv1**.
* Tipo de endereço. Selecione **IPv4**.
* A interface. Selecione a interface virada para o público ou para fora.
* Endereço IP local. Mantenha a predefinição.
* Tipo de endereço IP peer. Selecione **IP**.
* Endereço de pares. Introduza o endereço IP ip de pares vpn vpn primário.
* Autenticação. Selecione **chave pré-partilhada**.
* Chave pré-partilhada / Confirmar Chave Pré-partilhada. Introduza a tecla pré-partilhada para combinar com a tecla de gateway CloudSimple VPN.
* Identificação Local. Introduza o endereço IP público da firewall de Palo Alto no local.
* Identificação dos pares. Introduza o endereço IP ip de pares vpn vpn primário.

Separador Opções Avançadas:

* Ativar o modo passivo. deixe esta opção desselecionada.
* Ativa o NAT Traversal. Deixe descontrolado se a firewall de Palo Alto não estiver por trás de qualquer dispositivo NAT. Caso contrário, selecione a caixa de verificação.

IKEv1:

* Modo de Troca. Selecione **principal**.
* Perfil ike Crypto. Selecione o perfil IKE Crypto que criou anteriormente. Deixe a caixa de fragmentação ativa desmarcada.
* Deteção de pares mortos. Deixe a caixa desmarcada.

Repita os passos anteriores para criar o gateway IKE secundário.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definir perfis de Criptografia IPSEC

Selecione > Os perfis de**rede de expansão**da **rede** > **IPSEC Crypto** > **Add,** configure os seguintes campos e clique **EM OK**.

* O nome. Introduza um nome para o perfil criptodo IPsec.
* Protocolo IPsec. Selecione **ESP**.
* Encriptação. Clique em **Adicionar** e selecione o método de encriptação apropriado.
* Autenticação. Clique em **Adicionar** e selecione o método de autenticação apropriado.
* Grupo DH. Selecione **sem pfs**.
* Duração. Definir como 30 minutos.
* Ativar. Deixe a caixa desmarcada.

Repita os passos anteriores para criar outro perfil criptoigrafo IPsec, que será usado como o par vpn vpn secondary CloudSimple. O mesmo perfil IPSEC Crypto também pode ser utilizado para os túneis iPsec primários e secundários (ver o seguinte procedimento).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definir perfis de monitor para monitorização de túneis

Selecione **Network** > Expandir perfis de rede > **Adicionar,** > **Add**configurar os**seguintes**campos e clicar **EM OK**.

* O nome. Introduza qualquer nome do perfil Monitor a utilizar para monitorização do túnel para uma reação proactiva à falha.
* Ação. Selecione **Fail Over**.
* Intervalo. Introduza o valor **3**.
* O limiar. Introduza o valor **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Instale túneis IPsec primários e secundários.

Selecione > **Túneis IPsec** >  **da****rede,** configure os seguintes campos e clique em **OK**.

Separador geral:

* O nome. Introduza qualquer nome para o túnel ipsec primário a ser espreitado com o par vpn primário cloudSimple.
* Interface do túnel. Selecione a interface principal do túnel.
* Tipo, tipo. Mantenha a predefinição.
* Tipo de endereço. Selecione **IPv4**.
* IKE Gateway. Selecione o gateway principal IKE.
* Perfil criptografia IPsec. Selecione o perfil principal do IPsec. Selecione **Mostrar opções avançadas**.
* Ativar a proteção de reprodução. Mantenha a predefinição.
* Cabeçalho de TOS de cópia. Deixe a caixa desmarcada.
* Monitor de túneis. Verifique a caixa.
* IP destino. Introduza qualquer endereço IP pertencente à subnet CloudSimple Private Cloud que seja permitida sobre a ligação Site-a-Site. Certifique-se de que as interfaces do túnel (como o túnel.20 - 10.64.5.2/32 e o túnel.30 - 10.64.6.2/32) em Palo Alto podem chegar ao endereço IP CloudSimple Private Cloud sobre a VPN site-to-site. Consulte a seguinte configuração para identificações por procuração.
* O perfil. Selecione o perfil do monitor.

Separador de IDs proxy: Clique em **IPv4** > **Adicionar** e configurar o seguinte:

* Identidade de procuração. Insira qualquer nome para o tráfego interessante. Pode haver várias identificações por procuração transportadas dentro de um túnel iPsec.
* Local. Especifique as subredes locais no local que são autorizadas a comunicar com subredes de Nuvem Privada através da VPN site-to-site.
* É remoto. Especifique as subredes remotas Da Nuvem Privada que são autorizadas a comunicar com as subredes locais.
* O protocolo. Selecione **qualquer**.

Repita os passos anteriores para criar outro túnel IPsec para utilizar para o segundo par de VPN CloudSimple.

## <a name="references"></a>Referências

Configurar o NAT na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guia de configuração da série Cisco ASA 5500</a>

Atributos IKEv1 e IKEv2 suportados na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guia de configuração CLI série da Cisco ASA</a>

Configurar a VPN site-to-site do IPsec na Cisco ASA com a versão 8.4 e mais tarde:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configure os túneis sítio-a-local ikev1 iPsec com o ASDM ou o CLI na ASA</a>

Configurar o aparelho de segurança adaptável da Cisco (ASAv) em Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Guia de arranque rápido de segurança adaptável da Cisco (ASAv)</a>

Configurar VPN site-to-site com IDs proxy em Palo Alto:

[Configurar VPN site-to-site](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Criação de um monitor de túneis:

[Configurar a monitorização do túnel](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operações de gateway IKE ou iPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Ativar/Desativar, refrescar ou reiniciar um gateway IKE ou túnel IPsec</a>

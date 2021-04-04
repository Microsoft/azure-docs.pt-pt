---
title: Azure VMware Solution by CloudSimple - Configure alta disponibilidade de entrada no local para gateway VPN CloudSimple
description: Descreve como configurar uma ligação de alta disponibilidade do seu ambiente no local para um gateway VPN CloudSimple habilitado para alta disponibilidade
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895704"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Configure uma ligação de alta disponibilidade desde as instalações até ao gateway VPN cloudSimple

Os administradores de rede podem configurar uma ligação VPN site-to-site de alta disponibilidade do seu ambiente no local para um gateway VPN VPN cloudSimple.

Este guia apresenta passos para configurar uma firewall no local para uma ligação de alta disponibilidade do IPsec Site-to-Site. Os passos detalhados são específicos do tipo de firewall no local. Como exemplos, este guia apresenta passos para dois tipos de firewalls: Cisco ASA e Palo Alto Networks.

## <a name="before-you-begin"></a>Antes de começar

Complete as seguintes tarefas antes de configurar a firewall no local.

1. Verifique se a sua organização [fornece os](create-nodes.md) nós necessários e criou pelo menos uma CloudSimple Private Cloud.
2. [Configure uma porta de entrada VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre a sua rede no local e a sua CloudSimple Private Cloud.

Consulte [as propostas gerais das portas VPN](cloudsimple-vpn-gateways.md) para as propostas da fase 1 e da fase 2 apoiadas.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Configure no local firewall Cisco ASA

As instruções desta secção aplicam-se à versão CISCO ASA 8.4 e posterior. No exemplo de configuração, a Versão 9.10 do Software de Aparelhos de Segurança Adaptativa Cisco é implantada e configurada no modo IKEv1.

Para que a VPN local funcione, deve permitir o UDP 500/4500 e ESP (protocolo IP 50) a partir do IP público primário e secundário cloudSimple (PEER IP) na interface externa do gateway VPN Cisco ASA no local.

### <a name="1-configure-phase-1-ikev1"></a>1. Configurar a fase 1 (IKEv1)

Para ativar a fase 1 (IKEv1) na interface externa, insira o seguinte comando CLI na firewall Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Criar uma política IKEv1

Crie uma política IKEv1 que defina os algoritmos e métodos a serem usados para hashing, autenticação, Diffie-Hellman grupo, vida útil e encriptação.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Criar um grupo de túneis

Criar um grupo de túneis sob os atributos IPsec. Configure o endereço IP do par e a chave pré-partilhada do túnel, que definiu [ao configurar o seu gateway VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Configurar a fase 2 (IPsec)

Para configurar a fase 2 (IPsec), crie uma lista de controlo de acesso (ACL) que defina o tráfego a ser encriptado e escavado. No exemplo seguinte, o tráfego de juros provém do túnel que provém da sub-rede local (10.16.1.0/24) até à sub-rede remota Private Cloud (192.168.0.0/24). A ACL pode conter múltiplas entradas se houver várias sub-redes entre os sites.

Nas versões Cisco ASA 8.4 e posterior, podem ser criados grupos de objetos ou objetos que sirvam de recipientes para as redes, sub-redes, endereços IP do anfitrião ou vários objetos. Crie um objeto para o local e um objeto para as sub-redes remotas e use-os para as declarações de cripto ACL e NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Defina uma sub-rede local no local como um objeto

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Defina a sub-rede remota CloudSimple como um objeto

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Configurar uma lista de acesso para o tráfego de interesses

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Configurar o conjunto de transformação

Configure o conjunto de transformação (TS), que deve envolver a palavra-chave ```ikev1``` . Os atributos de encriptação e haxixe especificados na TS devem coincidir com os parâmetros listados na [configuração padrão para gateways VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configurar o mapa cripto

Configure o mapa cripto, que contém estes componentes:

* Endereço IP peer
* ACL definida que contém o tráfego de interesse
* Conjunto de transformação

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Aplicar o mapa cripto

Aplicar o mapa cripto na interface externa:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Confirmar as regras nat aplicáveis

Segue-se a regra NAT que é utilizada. Certifique-se de que o tráfego VPN não está sujeito a qualquer outra regra DA.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Amostra IPsec Site-to-Site VPN vpn estabelecido produção da Cisco ASA

Saída da fase 1:

![Saída da fase 1 para firewall Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Saída da fase 2:

![Saída da fase 2 para firewall Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Configurar no local firewall Palo Alto Networks

As instruções desta secção aplicam-se à versão 7.1 da Palo Alto Networks e posteriormente. Neste exemplo de configuração, palo Alto Networks VM-Series Versão de Software 8.1.0 é implantado e configurado no modo IKEv1.

Para que a VPN local funcione, deve permitir o UDP 500/4500 e ESP (protocolo IP 50) a partir do IP público primário e secundário cloudSimple (PEER IP) na interface externa do gateway palo Alto Networks.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Criar interfaces de túneis primários e secundários

Inscreva-se na firewall de Palo Alto, selecione **o**  >  Túnel de **Interfaces de** Rede  >    >  **Adicionar,** configurar os seguintes campos e clicar **EM OK**.

* Nome da interface. O primeiro campo é autopovoado com palavra-chave 'túnel'. No campo adjacente, introduza qualquer número entre 1 e 9999. Esta interface será usada como uma interface de túnel primário para transportar o tráfego site-a-local entre o datacenter no local e a Nuvem Privada.
* Um comentário. Introduza comentários para facilitar a identificação do propósito do túnel
* Perfil de fluxo de rede. Mantenha a predefinição.
* Config. Atribuir interface para: Router virtual: Selecionar **predefinição**. 
        Zona de Segurança: Selecione a zona para tráfego LAN confiável. Neste exemplo, o nome da zona para o tráfego LAN é "Confiança".
* IPv4. Clique **em Adicionar** e adicione qualquer endereço ip não sobreposto /32 no seu ambiente, que será atribuído à interface do túnel primário e será utilizado para monitorizar os túneis (explicados mais tarde).

Como esta configuração é para uma VPN de alta disponibilidade, são necessárias duas interfaces de túnel: uma primária e outra secundária. Repita os passos anteriores para criar a interface do túnel secundário. Selecione um ID de túnel diferente e um endereço ip diferente não uusado /32.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Criar rotas estáticas para sub-redes de nuvem privada a serem alcançadas sobre a VPN local-local

As rotas são necessárias para que as sub-redes no local cheguem às sub-redes de nuvem privada CloudSimple.

Selecione Routers virtuais **de rede**  >    >  *predefinidos*  >  **Rotas Estáticas**  >  **Adicionar,** configurar os seguintes campos e clicar **EM**.

* O nome. Insira qualquer nome para uma identificação fácil da finalidade da rota.
* Destino, destino. Especificar as sub-redes de nuvem privada CloudSimple a serem alcançadas através de interfaces de túneis S2S a partir do local
* A interface. Selecione a interface do túnel primário criada no passo 1 (Secção-2) a partir do dropdown. Neste exemplo, é o túnel.20.
* Próximo salto. Selecione **Nenhuma**.
* Distância Admin. Mantenha a predefinição.
* Métrica. Introduza qualquer valor de 1 a 65535. A chave é introduzir uma métrica mais baixa para a rota correspondente à interface do túnel primário em comparação com a interface de túnel secundário correspondente, tornando a rota anterior preferida. Se o túnel.20 tiver um valor métrico de 20 em oposição a um valor métrico de 30 para o túnel.30, o túnel.20 é preferido.
* Mesa de Rota. Mantenha a predefinição.
* Perfil BFD. Mantenha a predefinição.
* Monitorização de caminhos. deixe esta opção desselecionada.

Repita os passos anteriores para criar outra rota para as sub-redes Private Cloud usarem como uma rota secundária/backup através da interface de túnel secundário. Desta vez, selecione diferente identificação do túnel e uma métrica mais alta do que para a rota primária.

### <a name="3-define-the-cryptographic-profile"></a>3. Definir o perfil criptográfico

Defina um perfil criptográfico que especifica os protocolos e algoritmos para identificação, autenticação e encriptação a utilizar para a configuração de túneis VPN na Fase 1 do IKEv1.

Selecione **Redes**  >  **Expandir Perfis de Rede**  >  **IKE Crypto**  >  **Add**, configurar os seguintes campos e clicar **EM OK**.

* O nome. Insira qualquer nome do perfil cripto do IKE.
* Grupo DH. Clique **em Adicionar** e selecione o grupo DH apropriado.
* Encriptação. Clique **em Adicionar** e selecione o método de encriptação apropriado.
* Autenticação. Clique **em Adicionar** e selecione o método de autenticação apropriado.
* Vida útil chave. Mantenha a predefinição.
* IKEv2 Autenticação Múltipla. Mantenha a predefinição.

### <a name="4-define-ike-gateways"></a>4. Definir gateways IKE

Defina as portas do IKE para estabelecer a comunicação entre os pares em cada extremidade do túnel VPN.

Selecione **Redes**  >  **Expandir Perfis de Rede**  >  **IKE Gateways**  >  **Adicionar,** configurar os seguintes campos e clicar EM **OK**.

Separador geral:

* O nome. Insira o nome para a porta de entrada IKE para ser espreitado com o par VPN VPN de CloudSimple primário.
* Versão. Selecione **apenas o modo IKEv1**.
* Tipo de endereço. Selecione **IPv4**.
* A interface. Selecione a interface virada para o público ou exterior.
* Endereço IP local. Mantenha a predefinição.
* Tipo de endereço IP peer. Selecione **IP**.
* Endereço de pares. Insira o endereço IP peer VPN do CloudSimple.
* Autenticação. Selecione **Chave Pré-Partilhada**.
* Chave pré-partilhada / Confirmar Chave Pré-partilhada. Introduza a chave pré-partilhada para combinar com a tecla de gateway VPN CloudSimple.
* Identificação local. Insira o endereço IP público da firewall Palo Alto no local.
* Identificação de pares. Insira o endereço IP peer VPN do CloudSimple.

Separador de Opções Avançadas:

* Ativar o modo passivo. deixe esta opção desselecionada.
* Ativar o NAT Traversal. Deixe desmarcada se a firewall palo Alto não estiver por trás de qualquer dispositivo NAT. Caso contrário, selecione a caixa de verificação.

IKEv1:

* Modo de troca. Selecione **principal**.
* Perfil cripto do IKE. Selecione o perfil IKE Crypto que criou anteriormente. Deixe a caixa de fragmentação ativa sem controlo.
* Deteção de pares mortos. Deixe a caixa descontrolada.

Repita os passos anteriores para criar o gateway secundário do IKE.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definir perfis de cripto IPSEC

Selecione **Perfis**  >  **de Rede expandir perfis** de rede  >  **IPSEC Crypto**  >  **Add**, configurar os seguintes campos e clicar **EM OK**.

* O nome. Insira um nome para o perfil cripto IPsec.
* Protocolo IPsec. Selecione **ESP**.
* Encriptação. Clique **em Adicionar** e selecione o método de encriptação apropriado.
* Autenticação. Clique **em Adicionar** e selecione o método de autenticação apropriado.
* Grupo DH. Selecione **no-pfs**.
* Duração. Programado como 30 minutos.
* Ativar. Deixe a caixa descontrolada.

Repita os passos anteriores para criar outro perfil cripto IPsec, que será usado como o par VPN VPN secundário da CloudSimple. O mesmo perfil IPSEC Crypto também pode ser utilizado tanto para os túneis IPsec primários como secundários (ver procedimento a seguir).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definir perfis de monitor para monitorização do túnel

Selecione **Network**  >  **Expand Network Profiles**  >  **Monitor**  >  **Add**, configurar os seguintes campos e clicar **EM OK**.

* O nome. Introduza qualquer nome do perfil monitor a utilizar para monitorização do túnel para uma reação proactiva à falha.
* A ação. Selecione **'Fail Over'.**
* O intervalo. Introduza o valor **3**.
* O limiar. Introduza o valor **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Criar túneis IPsec primários e secundários.

Selecione   >  **Túneis IPsec de** rede  >  **Adicionar,** configurar os seguintes campos e clicar **EM OK**.

Separador geral:

* O nome. Insira qualquer nome para o túnel IPSEC primário a ser espreitado com o par VPN VPN primário da CloudSimple.
* Interface do túnel. Selecione a interface do túnel primário.
* Tipo. Mantenha a predefinição.
* Tipo de endereço. Selecione **IPv4**.
* IKE Gateway. Selecione o gateway primário do IKE.
* Perfil cripto do IPsec. Selecione o perfil principal do IPsec. Selecione **Mostrar Opções Avançadas**.
* Ativar a proteção contra repetições. Mantenha a predefinição.
* Copiar cabeçalho TOS. Deixe a caixa descontrolada.
* Monitor do túnel. Verifique a caixa.
* Destino IP. Introduza qualquer endereço IP pertencente à sub-rede CloudSimple Private Cloud que seja permitida sobre a ligação Site-a-Site. Certifique-se de que as interfaces do túnel (como o túnel.20 - 10.64.5.2/32 e o túnel.30 - 10.64.6.2/32) em Palo Alto podem chegar ao endereço CLOUDSimple Private Cloud IP sobre a VPN local-local. Consulte a seguinte configuração para identificação de procuração.
* Um perfil. Selecione o perfil do monitor.

Separador de IDs Proxy: Clique **em IPv4**  >  **Adicione** e configuure o seguinte:

* Identificação por procuração. Insira qualquer nome para o tráfego interessante. Pode haver várias identificações de Proxy dentro de um túnel IPsec.
* É local. Especifique as sub-redes locais no local que são autorizadas a comunicar com sub-redes Private Cloud sobre a VPN Site-to-Site.
* É remoto. Especifique as sub-redes remotas Private Cloud que são autorizadas a comunicar com as sub-redes locais.
* O protocolo. Selecione **qualquer**.

Repita os passos anteriores para criar outro túnel IPsec para utilizar para o par VPN VPN secundário cloudSimple.

## <a name="references"></a>Referências

Configurar o NAT na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Guia de configuração da série Cisco ASA 5500</a>

Atributos suportados IKEv1 e IKEv2 na Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Guia de configuração CLI da série Cisco ASA</a>

Configurar a VPN site-to-site do IPsec na Cisco ASA com a versão 8.4 e posterior:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Configure túneis iKEv1 IPsec site-to-site com o ASDM ou CLI na ASA</a>

Configurar o Cisco Adaptive Security Appliance virtual (ASAv) no Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Guia de arranque rápido de segurança adaptativa cisco (ASAv)</a>

Configurar a VPN site-to-site com IDs proxy em Palo Alto:

[Configurar VPN site-to-site](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Criação do monitor do túnel:

[Configurar monitorização do túnel](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operações de gateway IKE ou túnel IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Ativar/Desativar, refrescar ou reiniciar um gateway IKE ou um túnel IPsec</a>

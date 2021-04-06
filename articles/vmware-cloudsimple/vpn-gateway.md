---
title: Solução Azure VMware by CloudSimple - Configurar uma porta de entrada VPN
description: Descreve como configurar o gateway VPN ponto-a-local e o gateway VPN local-to-site e criar ligações entre a sua rede no local e a cloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 78f78bfbf885cbae7708a75cb54ce2d41e7b526c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899104"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configurar gateways VPN na rede CloudSimple

As gateways VPN permitem ligar-se à rede CloudSimple a partir da sua rede no local e a partir de um computador cliente remotamente. Uma ligação VPN entre a sua rede no local e a sua rede CloudSimple fornece acesso ao vCenter e cargas de trabalho na sua Nuvem Privada. O CloudSimple suporta as portas VPN do Site-a-Site e do Ponto-a-Local.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação **VPN site-to-site permite-lhe** configurar as suas cargas de trabalho private Cloud para aceder a serviços no local. Também pode utilizar no local o Ative Directory como fonte de identidade para autenticar o seu vCenter Private Cloud.  Atualmente, apenas o tipo **VPN baseado em políticas** é suportado.
* A ligação **VPN ponto-a-local** é a forma mais simples de ligar à sua Nuvem Privada a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar-se remotamente à Nuvem Privada. Para obter informações sobre a instalação de um cliente para uma ligação VPN ponto-a-local, consulte [configurar uma ligação VPN à sua Nuvem Privada](set-up-vpn.md).

Numa região, pode criar uma porta VPN ponto-a-local e uma porta VPN site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Adição automática de VLAN/subredes

As gateways VPN VPN da CloudSimple fornecem políticas para adicionar VLAN/subnets aos gateways VPN.  As políticas permitem especificar diferentes regras para a gestão VLAN/subnets e vlan/subnets definidos pelo utilizador.  As regras de gestão VLAN/subnets aplicam-se a quaisquer novas Nuvens Privadas que crie.  As regras para VLANs/sub-redes definidas pelo utilizador permitem-lhe adicionar automaticamente quaisquer novas VLAN/subnets às nuvens privadas existentes ou novas Para uma porta VPN site-to-site, define a política para cada ligação.

As políticas de adição de VLANs/sub-redes aos gateways VPN aplicam-se tanto aos gateways VPN do Site-Para-Local como ao Ponto-A-Local.

## <a name="automatic-addition-of-users"></a>Adição automática de utilizadores

Uma porta VPN ponto-a-local permite definir uma política de adição automática para novos utilizadores. Por padrão, todos os proprietários e colaboradores da subscrição têm acesso ao portal CloudSimple.  Os utilizadores são criados apenas quando o portal CloudSimple é lançado pela primeira vez.  A seleção de regras **de adicionar automaticamente** permite que qualquer novo utilizador aceda à rede CloudSimple utilizando a ligação VPN ponto-a-local.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Configurar um portal VPN site-to-site

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede.**
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração do Gateway**, especifique as seguintes definições e clique em **Seguinte**.

    * Selecione **VPN site-to-site** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Opcionalmente, ativar a Alta Disponibilidade.

    ![Criar porta VPN site-to-site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Ativar a Alta Disponibilidade requer que o seu dispositivo VPN no local suporte a ligação a dois endereços IP. Esta opção não pode ser desativada uma vez que o gateway VPN é implantado.

5. Crie a primeira ligação a partir da sua rede no local e clique em **Seguinte**.

    * Insira um nome para identificar a ligação.
    * Para o IP par, insira o endereço IP público do gateway VPN no local.
    * Introduza o identificador de pares do seu portal VPN no local.  O identificador de pares é geralmente o endereço IP público do seu gateway VPN no local.  Se configurar um identificador específico no seu portal, introduza o identificador.
    * Copie a chave partilhada para a ligação a partir do seu gateway VPN no local.  Para alterar a chave partilhada padrão e especificar uma nova, clique no ícone de edição.
    * Para **Prefixos no local,** introduza os prefixos CIDR no local que acederão à rede CloudSimple.  Pode adicionar vários prefixos CIDR quando criar a ligação.

    ![Criar ligação de gateway VPN site-to-site](media/create-vpn-gateway-s2s-connection.png)

6. Ativar as sub-redes VLAN/sub-redes da sua rede Private Cloud que serão acedidas a partir da rede no local e clique em **Seguinte**.

    * Para adicionar uma gestão VLAN/sub-rede, ative **adicionar VLANs/Subnets de Nuvens Privadas de Gestão**.  A sub-rede de gestão é necessária para sub-redes vMotion e vSAN.
    * Para adicionar sub-redes vMotion, ative **adicionar a rede vMotion de Nuvens Privadas**.
    * Para adicionar sub-redes vSAN, ative **adicionar a sub-rede vSAN de Nuvens Privadas**.
    * Selecione ou desescotre VLANs específicos.

    ![Criar ligação](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Reveja as definições e clique **em Enviar por isso.**

    ![Revisão de gateway VPN site-to-site e criar](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Criar gateway VPN ponto-a-local

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede.**
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração do Gateway**, especifique as seguintes definições e clique em **Seguinte**.

    * Selecione **Point-to-Site VPN** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway Ponto-a-Local.  Os endereços DHCP serão dados a partir da sub-rede do cliente quando se ligar.

5. Para **Ligação/Utilizador**, especifique as seguintes definições e clique em **Seguinte**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Nuvem Privada através do gateway Ponto-a-Local, selecione **Adicionar automaticamente todos os utilizadores**. Quando seleciona a opção, todos os utilizadores da lista de utilizadores são automaticamente selecionados. Pode anular a opção automática desseleccionando utilizadores individuais na lista.
    * Para selecionar utilizadores individuais, clique nas caixas de verificação na lista de utilizadores.

6. A secção VLANs/Subnets permite especificar VLANs/subnets de gestão e utilizador para o gateway e conexões.

    * As opções **de adicionar automaticamente** definem a política global para o gateway. As definições aplicam-se ao gateway atual. As definições podem ser ultrapassadas na área **Select.**
    * **Selecione Adicionar VLANs/Subnets de Nuvens Privadas .** 
    * Para adicionar todos os VLANs/subnets definidos pelo utilizador, clique em  **Adicionar VLANs/Subnets definidos pelo utilizador**.
    * As definições **Select** sobrepõem-se às definições globais em **adicionar automaticamente**.

7. Clique **em Seguinte** para rever as definições. Clique nos ícones editar para escoar quaisquer alterações.
8. Clique em **Criar** para criar o gateway VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Sub-rede de clientes e protocolos para gateway VPN ponto-a-local

O gateway VPN ponto-a-local permite ligações TCP e UDP.  Escolha o protocolo a utilizar quando ligar a partir do seu computador selecionando a configuração TCP ou UDP.

A sub-rede de clientes configurada é utilizada tanto para clientes TCP como UDP.  O prefixo CIDR é dividido em duas sub-redes, uma para TCP e outra para clientes UDP. Escolha a máscara de prefixo com base no número de utilizadores VPN que se ligarão simultaneamente.  

A tabela que se segue lista o número de ligações simultâneas do cliente para máscara de prefixo.

| Máscara de prefixo | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Número de ligações TCP simultâneas | 124 | 60 | 28 | 12 | 4 |
| Número de ligações UDP simultâneas | 124 | 60 | 28 | 12 | 4 |

Para ligar utilizando VPN ponto-a-local, consulte [Connect to CloudSimple usando VPN ponto-a-local](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).

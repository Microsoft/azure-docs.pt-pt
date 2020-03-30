---
title: Solução Azure VMware by CloudSimple - Configurar um gateway VPN
description: Descreve como configurar o gateway VPN ponto-a-site e o gateway VPN site-to-site e criar ligações entre a sua rede no local e a sua CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279497"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Configurar gateways VPN na rede CloudSimple

Os gateways VPN permitem-lhe ligar-se à rede CloudSimple a partir da sua rede no local e a partir de um computador cliente remotamente. Uma ligação VPN entre a sua rede no local e a sua rede CloudSimple fornece acesso ao vCenter e às cargas de trabalho na sua Cloud Privada. A CloudSimple suporta os portões VPN do Site-para-Site e do Ponto-a-Site VPN.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação VPN do **site-para-site** permite-lhe configurar as suas cargas de trabalho da Cloud Privada para aceder aos serviços no local. Também pode utilizar o Ative Directory no local como fonte de identidade para autenticar o seu vCenter Private Cloud.  Atualmente, apenas o tipo **VPN baseado em políticas** é suportado.
* A ligação **VPN ponto-a-local** é a forma mais simples de se ligar à sua Nuvem Privada a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar remotamente à Nuvem Privada. Para obter informações sobre a instalação de um cliente para uma ligação VPN Ponto-a-Site, consulte [Configurar uma ligação VPN à sua Nuvem Privada](set-up-vpn.md).

Numa região, pode criar um gateway VPN Ponto-a-Local e um portal VPN site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Adição automática de VLAN/subnets

Os gateways CloudSimple VPN fornecem políticas para adicionar VLAN/subnets aos gateways VPN.  As políticas permitem especificar diferentes regras para a gestão VLAN/subnets e vlan/subnets definidas pelo utilizador.  As regras de gestão VLAN/subnets aplicam-se a quaisquer novas Nuvens Privadas que crie.  As regras para VLANs/subnets definidas pelo utilizador permitem adicionar automaticamente quaisquer novos VLAN/subnets às nuvens privadas existentes ou novas Para um gateway VPN site-to-site, define a política para cada ligação.

As políticas de adição de VLANs/subnets aos gateways VPN aplicam-se tanto aos gateways VPN do Site para o Local como aos gateways VPN ponto-a-local.

## <a name="automatic-addition-of-users"></a>Adição automática de utilizadores

Um gateway VPN Ponto-a-Site permite-lhe definir uma política de adição automática para novos utilizadores. Por padrão, todos os proprietários e colaboradores da subscrição têm acesso ao portal CloudSimple.  Os utilizadores só são criados quando o portal CloudSimple for lançado pela primeira vez.  Selecionar **automaticamente adicione** regras permite que qualquer novo utilizador aceda à rede CloudSimple utilizando a ligação VPN Ponto-a-Local.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Criar um gateway VPN site-to-site

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração gateway,** especifique as seguintes definições e clique **em Seguinte**.

    * Selecione **VPN site-to-site** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Opcionalmente, ativar Alta Disponibilidade.

    ![Criar gateway VPN site-to-site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Ativar a Alta Disponibilidade requer que o seu dispositivo VPN no local suporte a ligação a dois endereços IP. Esta opção não pode ser desativada uma vez que o gateway VPN é implantado.

5. Crie a primeira ligação a partir da sua rede no local e clique em **Next**.

    * Insira um nome para identificar a ligação.
    * Para o IP peer, insira o endereço IP público do seu gateway VPN no local.
    * Introduza o identificador de pares do seu gateway VPN no local.  O identificador de pares é geralmente o endereço IP público do seu gateway VPN no local.  Se configurar um identificador específico no seu portal, introduza o identificador.
    * Copie a chave partilhada para utilizar para a ligação a partir do seu gateway VPN no local.  Para alterar a chave partilhada por defeito e especificar uma nova, clique no ícone de edição.
    * Para **prefixos no local,** introduza os prefixos CIDR no local que acederão à rede CloudSimple.  Pode adicionar vários prefixos CIDR quando criar a ligação.

    ![Criar a ligação de gateway VPN site-to-site](media/create-vpn-gateway-s2s-connection.png)

6. Ative as vLAN/subnets na sua rede Private Cloud que serão acedidas a partir da rede no local e clique em **Next**.

    * Para adicionar uma vlan/sub-rede de gestão, ative **adicionar gestão VLANs/Subnets de Nuvens Privadas**.  A sub-rede de gestão é necessária para as subredes vMotion e vSAN.
    * Para adicionar subredes vMotion, ative **adicionar rede vMotion de Nuvens Privadas**.
    * Para adicionar subredes vSAN, ative **adicionar a sub-rede vSAN de nuvens privadas**.
    * Selecione ou desselecione VLANs específicos.

    ![Criar ligação](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Reveja as definições e clique em **Submeter**.

    ![Revisão de gateway vpn site-to-site e criar](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Criar gateway VPN ponto-a-local

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração gateway,** especifique as seguintes definições e clique **em Seguinte**.

    * Selecione **VPN ponto-a-local** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway Ponto-a-Local.  Os endereços DHCP serão dados da subnet do cliente quando se ligar.

5. Para **ligar/utilizador,** especifique as seguintes definições e clique **em Seguinte**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Cloud Privada através do gateway Ponto-a-Site, selecione **adicionar automaticamente todos os utilizadores**. Quando seleciona a opção, todos os utilizadores da lista de utilizadores são automaticamente selecionados. Pode anular a opção automática desmarcando utilizadores individuais na lista.
    * Para selecionar utilizadores individuais, clique nas caixas de verificação na lista de utilizadores.

6. A secção VLANs/Subnets permite especificar a gestão e o utilizador VLANs/subnets para o gateway e ligações.

    * As opções **de adicionar automaticamente** definiram a política global para o gateway. As definições aplicam-se ao gateway atual. As definições podem ser ultrapassadas na área **Select.**
    * **Selecione Adicionar gestão VLANs/Subnets de Nuvens Privadas**. 
    * Para adicionar todos os VLANs/subredes definidos pelo utilizador, clique em **Adicionar VLANs/Subnets definidos pelo utilizador**.
    * As definições **Select** sobrepõem as definições globais sob **adicionar automaticamente**.

7. Clique em **Seguir** para rever as definições. Clique nos ícones Editar para efazer quaisquer alterações.
8. Clique em **Criar** o gateway VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Subnet do cliente e protocolos para gateway VPN Ponto-a-Site

O gateway VPN Ponto-a-Local permite ligações TCP e UDP.  Escolha o protocolo a utilizar quando se conecta a partir do computador selecionando a configuração TCP ou UDP.

A sub-rede de clientes configurada é utilizada tanto para clientes TCP como UDP.  O prefixo CIDR é dividido em duas subredes, uma para TCP e outra para clientes UDP. Escolha a máscara de prefixo com base no número de utilizadores vpN que se conectarão simultaneamente.  

A tabela seguinte lista o número de ligações simultâneas do cliente para máscara de prefixo.

| Máscara de Prefixo | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Número de ligações tcp simultâneas | 124 | 60 | 28 | 12 | 4 |
| Número de ligações uDP simultâneas | 124 | 60 | 28 | 12 | 4 |

Para ligar utilizando VPN ponto-a-local, consulte [Connect to CloudSimple utilizando VPN ponto-a-site](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).

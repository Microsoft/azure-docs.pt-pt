---
title: Azure VMware Solutions (AVS) - Criar um gateway VPN
description: Descreve como configurar o gateway VPN ponto-a-site e o gateway VPN site-to-site e criar ligações entre a sua rede no local e a sua Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8731f7a9ff7f2cab7516e43c62ddc1aac2668168
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016770"
---
# <a name="set-up-vpn-gateways-on-avs-network"></a>Configurar gateways VPN na rede AVS

Os gateways VPN permitem-lhe ligar à rede AVS a partir da sua rede no local e a partir de um computador cliente remotamente. Uma ligação VPN entre a sua rede no local e a sua rede AVS fornece acesso ao vCenter e cargas de trabalho na sua Nuvem Privada AVS. A AVS suporta os portões VPN do Site-para-Site e do Ponto-a-Local VPN.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação **VPN do site-para-site** permite-lhe configurar as suas cargas de trabalho da Nuvem Privada AVS para aceder aos serviços no local. Também pode utilizar o Ative Directory no local como fonte de identidade para autenticação no seu VCenter De Nuvem Privada AVS. Atualmente, apenas o tipo **VPN baseado em políticas** é suportado.
* A ligação **VPN ponto-a-local** é a forma mais simples de se ligar à sua Nuvem Privada AVS a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar remotamente à Nuvem Privada AVS. Para obter informações sobre a instalação de um cliente para uma ligação VPN Ponto-a-Site, consulte [Configurar uma ligação VPN à sua Nuvem Privada AVS](set-up-vpn.md).

Numa região, pode criar um gateway VPN Ponto-a-Local e um portal VPN site-to-site.

## <a name="automatic-addition-of-vlansubnets"></a>Adição automática de VLAN/subnets

Os gateways AVS VPN fornecem políticas para adicionar VLAN/subnets aos gateways VPN. As políticas permitem especificar diferentes regras para a gestão VLAN/subnets e vlan/subnets definidas pelo utilizador. As regras de gestão VLAN/subnets aplicam-se a quaisquer novas Nuvens Privadas AVS que crie. As regras para VLANs/subnets definidas pelo utilizador permitem adicionar automaticamente quaisquer novas VLAN/subnets às nuvens privadas existentes ou novas AVS Para um gateway VPN site-to-site, define a política para cada ligação.

As políticas de adição de VLANs/subnets aos gateways VPN aplicam-se tanto aos gateways VPN do Site para o Local como aos gateways VPN ponto-a-local.

## <a name="automatic-addition-of-users"></a>Adição automática de utilizadores

Um gateway VPN Ponto-a-Site permite-lhe definir uma política de adição automática para novos utilizadores. Por predefinição, todos os proprietários e colaboradores da subscrição têm acesso ao portal AVS. Os utilizadores só são criados quando o portal AVS é lançado pela primeira vez. Selecionar **Automaticamente adicione** regras permite que qualquer novo utilizador aceda à rede AVS utilizando a ligação VPN Ponto-a-Local.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Criar um gateway VPN site-to-site

1. [Aceda ao portal AVS](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN site-to-site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione a localização Azure onde o seu serviço AVS está implantado.
    * Opcionalmente, ativar Alta Disponibilidade.

    ![Criar gateway VPN site-to-site](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Ativar a Alta Disponibilidade requer que o seu dispositivo VPN no local suporte a ligação a dois endereços IP. Esta opção não pode ser desativada uma vez que o gateway VPN é implantado.

5. Crie a primeira ligação a partir da sua rede no local e clique em **Next**.

    * Insira um nome para identificar a ligação.
    * Para o IP peer, insira o endereço IP público do seu gateway VPN no local.
    * Introduza o identificador de pares do seu gateway VPN no local. O identificador de pares é geralmente o endereço IP público do seu gateway VPN no local. Se configurar um identificador específico no seu portal, introduza o identificador.
    * Copie a chave partilhada para utilizar para a ligação a partir do seu gateway VPN no local. Para alterar a chave partilhada por defeito e especificar uma nova, clique no ícone de edição.
    * Para **prefixos no local,** introduza os prefixos CIDR no local que acederão à rede AVS. Pode adicionar vários prefixos CIDR quando criar a ligação.

    ![Criar a ligação de gateway VPN site-to-site](media/create-vpn-gateway-s2s-connection.png)

6. Ative as vLAN/subnets na sua rede AVS Private Cloud que será acedida a partir da rede no local e clique em **Next**.

    * Para adicionar uma vlan/sub-rede de gestão, ative **adicionar gestão VLANs/Subnets de Nuvens Privadas AVS**. A sub-rede de gestão é necessária para as subredes vMotion e vSAN.
    * Para adicionar subredes vMotion, ative **adicionar rede vMotion de Nuvens Privadas AVS**.
    * Para adicionar subredes vSAN, ative **adicionar a sub-rede vSAN de nuvens privadas**.
    * Selecione ou desselecione VLANs específicos.

    ![Criar ligação](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Reveja as definições e clique em **Submeter**.

    ![Revisão de gateway vpn site-to-site e criar](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Criar gateway VPN ponto-a-local

1. [Aceda ao portal AVS](access-cloudsimple-portal.md) e selecione **Rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN ponto a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione a localização Azure onde o seu serviço AVS está implantado.
    * Especifique a sub-rede do cliente para o gateway ponto a site. Os endereços DHCP serão dados da subnet do cliente quando se ligar.

5. Para **conexão/usuário**, especifique as seguintes configurações e clique em **Avançar**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Nuvem Privada AVS através do gateway Ponto-a-Site, selecione **adicionar automaticamente todos os utilizadores**. Quando seleciona a opção, todos os utilizadores da lista de utilizadores são automaticamente selecionados. Você pode substituir a opção automática desmarcando usuários individuais na lista.
    * Para selecionar utilizadores individuais, clique nas caixas de verificação na lista de utilizadores.

6. A seção VLANs/sub-redes permite especificar VLANs de gerenciamento e de usuário/sub-redes para o gateway e as conexões.

    * As opções **de adicionar automaticamente** definiram a política global para o gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área de **seleção** .
    * **Selecione Adicionar gestão VLANs/Subnets de Nuvens Privadas AVS**. 
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique em **Adicionar VLANs/sub-redes definidas pelo usuário**.
    * As configurações de **selecionar** substituem as configurações globais em **adicionar automaticamente**.

7. Clique em **Avançar** para revisar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway de VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Subnet do cliente e protocolos para gateway VPN Ponto-a-Site

O gateway VPN Ponto-a-Local permite ligações TCP e UDP. Escolha o protocolo a utilizar quando se conecta a partir do computador selecionando a configuração TCP ou UDP.

A sub-rede de clientes configurada é utilizada tanto para clientes TCP como UDP. O prefixo CIDR é dividido em duas subredes, uma para TCP e outra para clientes UDP. Escolha a máscara de prefixo com base no número de utilizadores vpN que se conectarão simultaneamente. 

A tabela seguinte lista o número de ligações simultâneas do cliente para máscara de prefixo.

| Máscara de Prefixo | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Número de ligações tcp simultâneas | 124 | 60 | 28 | 12 | 4 |
| Número de ligações uDP simultâneas | 124 | 60 | 28 | 12 | 4 |

Para ligar utilizando VPN ponto-a-local, consulte [O Connect to AVS utilizando VPN ponto-a-local](set-up-vpn.md#connect-to-avs-using-point-to-site-vpn).

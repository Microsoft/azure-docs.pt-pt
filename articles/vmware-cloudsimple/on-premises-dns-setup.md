---
title: Soluções Azure VMware (AVS) - Configure DNS para Nuvem Privada AVS
description: Descreve como configurar a resolução de nome sns para acesso ao servidor vCenter numa Nuvem Privada AVS a partir de estações de trabalho no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eadedcea7e6010cf93d118b3781630053609d29f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019609"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configure DNS para resolução de nome saque para acesso a DVS Private Cloud vCenter a partir de estações de trabalho no local

Para aceder ao servidor vCenter numa Nuvem Privada AVS a partir de estações de trabalho no local, deve configurar a resolução de endereços DNS para que o servidor vCenter possa ser abordado pelo nome de anfitrião, bem como pelo endereço IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Obtenha o endereço IP do servidor DNS para a sua Nuvem Privada AVS

1. Inscreva-se no [portal AVS.](access-cloudsimple-portal.md)

2. Navegue para **Recursos** > **Nuvens Privadas AVS** e selecione a Nuvem Privada AVS a que pretende ligar.

3. Na página **resumo** da Nuvem Privada AVS em **Informações Básicas,** copie o endereço IP do servidor DNS private Cloud DNS.

    ![Servidores DNS dNS privados da Nuvem AVS](media/private-cloud-dns-server.png)


Utilize qualquer uma destas opções para a configuração DNS.

* [Crie uma zona no servidor DNS para *. AVS.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Crie um avançado condicionado no seu servidor DNS no local para resolver *. AVS.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-avsio"></a>Crie uma zona no servidor DNS para *. AVS.io

Você pode configurar uma zona como uma zona de stub e apontar para os servidores DNS na Nuvem Privada para resolução de nome. Esta secção fornece informações sobre a utilização de um servidor BIND DNS ou de um servidor DNS do Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Criar uma zona num servidor BIND DNS

O ficheiro e os parâmetros específicos para configurar podem variar em função da configuração individual do DNS.

Por exemplo, para a configuração do servidor BIND predefinido, edite/etc/nomeado.conf no seu servidor DNS e adicione as seguintes informações de zona.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Criar uma zona num servidor DNS do Microsoft Windows

1. Clique no servidor DNS e selecione **New Zone**. 
  
    ![Zona Nova](media/DNS01.png)
2. Selecione **Stub Zone** e clique **em Seguinte**.

    ![Zona Nova](media/DNS02.png)
3. Selecione a opção adequada dependendo do seu ambiente e clique em **Next**.

    ![Zona Nova](media/DNS03.png)
4. Selecione zona de **lookup Para a Frente** e clique **em Next**.

    ![Zona Nova](media/DNS01.png)
5. Introduza o nome da zona e clique **em Seguinte**.

    ![Zona Nova](media/DNS05.png)
6. Introduza os endereços IP dos servidores DNS para a sua Nuvem Privada AVS que obteve no portal AVS.

    ![Zona Nova](media/DNS06.png)
7. Clique em **Seguinte,** conforme necessário para completar a configuração do assistente.

## <a name="create-a-conditional-forwarder"></a>Criar um avançado condicional

Um avançado condicional reencaminha todos os pedidos de resolução de nomes DNS para o servidor designado. Com esta configuração, qualquer pedido para *. AVS.io é encaminhado para os servidores DNS localizados na Nuvem Privada AVS. Os exemplos seguintes mostram como configurar os avançados em diferentes tipos de servidores DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Criar um avançado condicional num servidor BIND DNS

O ficheiro e os parâmetros específicos para configurar podem variar em função da configuração individual do DNS.

Por exemplo, para a configuração do servidor BIND predefinido, edite/etc/nomeado.conf no seu servidor DNS e adicione as seguintes informações de reencaminhamento condicional.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Criar um avançado condicional num servidor DNS do Microsoft Windows

1. Abra o Gestor DNS no servidor DNS.
2. Clique no direito dos **Avançados Condicionais** e selecione a opção de adicionar um novo avançado condicional.

    ![Forwarder Condicionado 1 Windows DNS](media/DNS08.png)
3. Introduza o domínio DNS e o endereço IP dos servidores DNS na Nuvem Privada AVS e clique em **OK**.

---
title: Solução Azure VMware by CloudSimple - Configure DNS para CloudSimple Private Cloud
description: Descreve como configurar a resolução de nome DNS para acesso ao servidor vCenter numa CloudSimple Private Cloud a partir de estações de trabalho no local
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899257"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configure DNS para resolução de nomes para acesso private Cloud vCenter a partir de estações de trabalho no local

Para aceder ao servidor vCenter numa CloudSimple Private Cloud a partir de estações de trabalho no local, tem de configurar a resolução de endereços DNS para que o servidor vCenter possa ser endereçado pelo nome de anfitrião, bem como pelo endereço IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Obtenha o endereço IP do servidor DNS para a sua Nuvem Privada

1. Inscreva-se no [portal CloudSimple](access-cloudsimple-portal.md).

2. Navegue para  >  **Recursos Nuvens Privadas** e selecione a Nuvem Privada a que pretende ligar.

3. Na página **Sumária** da Nuvem Privada em **Informações Básicas,** copie o endereço IP do servidor DNS private Cloud.

    ![Servidores PRIVADOs cloud DNS](media/private-cloud-dns-server.png)


Utilize qualquer uma destas opções para a configuração do DNS.

* [Crie uma zona no servidor DNS para *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Crie um reencaminhador condicional no seu servidor DNS no local para resolver *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Crie uma zona no servidor DNS para *.cloudsimple.io

Pode configurar uma zona como zona de canto e apontar para os servidores DNS na Nuvem Privada para resolução de nomes. Esta secção fornece informações sobre a utilização de um servidor BIND DNS ou de um servidor DNS do Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Criar uma zona num servidor BIND DNS

O ficheiro específico e os parâmetros para configurar podem variar em função da configuração individual do DNS.

Por exemplo, para a configuração padrão do servidor BIND, edite /etc/nomeado.conf no seu servidor DNS e adicione as seguintes informações de zona.

> [!NOTE]
>Este artigo contém referências ao termo escravo, um termo que a Microsoft já não usa. Quando o termo for removido do software, vamos removê-lo deste artigo.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Criar uma zona num servidor DNS do Microsoft Windows

1. Clique com o botão direito no servidor DNS e selecione **New Zone**. 
  
    ![Screenshot que destaca a opção do menu New Zone.](media/DNS01.png)
2. Selecione **Stub Zone** e clique em **Seguinte**.

    ![Screenshot que realça a opção Stub Zone.](media/DNS02.png)
3. Selecione a opção adequada dependendo do seu ambiente e clique em **Seguinte**.

    ![Screenshot que mostra as opções de replicação de dados da zona.](media/DNS03.png)
4. Selecione **para a frente zona de procuração** e clique em **Seguinte**.

    ![Screenshot que realça a opção zona de procuração forward.](media/DNS01.png)
5. Introduza o nome da zona e clique em **Seguinte**.

    ![Screenshot que mostra onde introduzir o nome da zona.](media/DNS05.png)
6. Introduza os endereços IP dos servidores DNS para a sua Cloud Privada que obteve a partir do portal CloudSimple.

    ![Nova Zona](media/DNS06.png)
7. Clique em **seguida,** conforme necessário para completar a configuração do assistente.

## <a name="create-a-conditional-forwarder"></a>Criar um avançado condicional

Um reencaminhador condicional remete todos os pedidos de resolução de nomes DNS para o servidor designado. Com esta configuração, qualquer pedido para *.cloudsimple.io é reencaminhado para os servidores DNS localizados na Nuvem Privada. Os exemplos a seguir mostram como configurar os reencaminhadores em diferentes tipos de servidores DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Criar um reencaminhador condicional num servidor BIND DNS

O ficheiro específico e os parâmetros para configurar podem variar em função da configuração individual do DNS.

Por exemplo, para a configuração padrão do servidor BIND, edite /etc/nomeado.conf no seu servidor DNS e adicione as seguintes informações de encaminhamento condicional.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Criar um reencaminhador condicional num servidor DNS do Microsoft Windows

1. Abra o DNS Manager no servidor DNS.
2. **Click Right-Click Compromes condicionados** e selecione a opção de adicionar um novo reencaminhador condicional.

    ![Avançado Condicional 1 DNS windows](media/DNS08.png)
3. Introduza o domínio DNS e o endereço IP dos servidores DNS na Nuvem Privada e clique em **OK**.

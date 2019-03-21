---
title: Opções de cablagem do Azure Data Box | Microsoft Docs
description: Descreve as várias opções de cablagem do Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/24/2018
ms.author: alkohli
ms.openlocfilehash: 4759676eff03606998759a586f758b7a26ead23d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077552"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Opções de cablagem do Azure Data Box

Este artigo descreve as várias formas de ligar o Azure Data Box para transferência de dados.

## <a name="transfer-via-mgmt-port"></a>Transferir através da porta MGMT

Esta opção é a configuração mínima absoluta para o Data Box. Apenas pode configurar a porta MGMT para gestão e dados.

Antes de começar, certifique-se de que tem:

- Um cabo Ethernet RJ45 para a porta MGMT.
- Uma origem de dados com um [SO suportado](data-box-system-requirements.md#supported-operating-systems-for-clients).

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Utilize um cabo RJ45 para ligar a porta MGMT ao servidor que tem os dados.

    ![Configuração de uma porta](media/data-box-cable-options/cabling-mgmt-only.png)

2. No servidor, defina:

    - O **endereço IP** como 192.168.100.5
    - A **sub-rede** como 255.255.255.0

3. Acesso local web da interface do Usuário do dispositivo em: 192.168.100.10. Inicie sessão e desbloqueie o Data Box com a palavra-passe de desbloqueio no portal do Azure.


## <a name="transfer-via-data-port-with-static-ips"></a>Transferir através da porta DATA com IPs estáticos

Pode configurar duas portas para o Data Box: a porta MGMT para tráfego de gestão e uma das portas de dados para dados. As portas de dados podem ser DATA 1, DATA 2 ou DATA 3.

Recomendamos vivamente que, se configurar apenas uma porta de dados, esta deve ser uma porta de 10 GbE, como DATA 1 ou DATA 2. Uma porta de 1 GbE aumentaria substancialmente o tempo necessário para a transferência de dados.

Antes de começar, certifique-se de que tem:

- Um cabo Ethernet RJ45 para a porta MGMT.
- Um cabo de cobre SFP+ Twinax de 10 GbE para cada porta de dados de 10 GbE que quer ligar.
- Uma ou mais origens de dados com um [SO suportado](data-box-system-requirements.md#supported-operating-systems-for-clients).

### <a name="option-1---initial-setup-via-server"></a>Opção 1 – Configuração inicial através do servidor

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Utilize um cabo Ethernet RJ45 do servidor diretamente para a porta MGMT para configuração.
2. Utilize um cabo RJ45 para DATA 3 ou SFP+ para ligar DATA 1 ou DATA 2 ao servidor que atua como origem de dados. Recomendamos que utilize as portas DATA 1 ou DATA 2 de 10 GbE para um bom desempenho.
3. No servidor, defina:

   - O **endereço IP** como 192.168.100.5
   - A **sub-rede** como 255.255.255.0

     ![Configuração de duas portas](media/data-box-cable-options/cabling-2-port-setup.png)

3. Acesso local web da interface do Usuário do dispositivo em: 192.168.100.10. Inicie sessão e desbloqueie o Data Box com a palavra-passe de desbloqueio no portal do Azure.
4. Atribua IPs estáticos às portas de dados que configurou.

### <a name="option-2---initial-setup-via-separate-computer"></a>Opção 2 – Configuração inicial através de um servidor separado

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Utilize um cabo Ethernet RJ45 do servidor diretamente para a porta MGMT para configuração.
2. Utilize um cabo RJ45 para DATA 3 ou SFP+ para ligar DATA 1 ou DATA 2 ao servidor. Recomendamos que utilize as portas DATA 1 ou DATA 2 de 10 GbE para um bom desempenho. As portas de dados estão ligadas através de um comutador de 10 GbE ao servidor com dados.
3. Configure o adaptador Ethernet do portátil que está a utilizar para ligar ao dispositivo com:

   - Um **endereço IP** 192.168.100.5
   - Uma **sub-rede** 255.255.255.0.

     ![Configuração de duas portas com um comutador](media/data-box-cable-options/cabling-with-static-ip.png)

3. Acesso local web da interface do Usuário do dispositivo em: 192.168.100.10. Inicie sessão e desbloqueie o Data Box com a palavra-passe de desbloqueio no portal do Azure.
4. Identifique os endereços IP atribuídos pelo servidor DHCP.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Transferir através da porta DATA com IPs estáticos através de um comutador 

Utilize esta configuração para múltiplas origens de dados em redes de 1 e 10 GbE.

Antes de começar, certifique-se de que tem:

- Um cabo Ethernet RJ45 para a porta MGMT.
- Um cabo de cobre SFP+ Twinax de 10 GbE para cada porta de dados de 10 GbE que quer ligar.
- Uma ou mais origens de dados com um [SO suportado](data-box-system-requirements.md#supported-operating-systems-for-clients). Estas origens de dados podem estar em redes diferentes, como redes de 1 ou 10 GbE.

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Utilize um cabo Ethernet RJ45 do servidor diretamente para a porta MGMT para configuração.
2. Utilize um cabo RJ45 para DATA 3 ou SFP+ para ligar DATA 1 ou DATA 2 ao servidor. Recomendamos que utilize as portas DATA 1 ou DATA 2 de 10 GbE para um bom desempenho.
3. Configure o adaptador Ethernet do portátil que está a utilizar para ligar ao dispositivo com:

   - Um **endereço IP** 192.168.100.5
   - Uma **sub-rede** 255.255.255.0.

     ![Configuração de duas portas com um comutador](media/data-box-cable-options/cabling-with-switch-static-ip.png)

3. Acesso local web da interface do Usuário do dispositivo em: 192.168.100.10. Inicie sessão e desbloqueie o Data Box com a palavra-passe de desbloqueio no portal do Azure.
4. Atribua IPs estáticos às portas de dados que configurou.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>Transferir através da porta DATA num ambiente DHCP

Utilize esta configuração se o dispositivo estiver num ambiente DHCP.

Antes de começar, certifique-se de que tem:

- Um cabo RJ45 se quiser ligar DATA 1.
- Um cabo de cobre SFP+ Twinax de 10 GbE para cada porta de dados de 10 GbE que quer ligar.
- Uma ou mais origens de dados com um [SO suportado](data-box-system-requirements.md#supported-operating-systems-for-clients). Estas origens de dados podem estar em redes diferentes, como redes de 1 ou 10 GbE.

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Utilize um cabo RJ45 ou SFP+ através de um comutador (em que esteja acessível um servidor DHCP) ligado ao servidor.

    ![Configuração de duas portas com um comutador](media/data-box-cable-options/cabling-dhcp-data-only.png)
2. Utilize um servidor DHCP ou DNS para identificar o endereço IP.
3. A partir de um servidor na mesma rede, aceda à UI Web local do dispositivo com o endereço IP atribuído pelo servidor DHCP. Inicie sessão e desbloqueie o Data Box com a palavra-passe de desbloqueio no portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Depois de instalar os cabos do dispositivo, aceda a [Copiar dados para o Azure Data Box](data-box-deploy-copy-data.md).

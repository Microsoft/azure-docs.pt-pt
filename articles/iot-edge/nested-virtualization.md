---
title: Virtualização aninhada para Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Saiba como navegar na virtualização aninhada em Azure IoT Edge para Linux no Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609656"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualização aninhada para Azure IoT Edge para Linux no Windows
Existem duas formas de virtualização aninhada compatíveis com a Azure IoT Edge para Linux no Windows. Os utilizadores podem optar por implementar através de um VM local ou VM Azure. Este artigo irá fornecer aos utilizadores clareza sobre qual a melhor opção para o seu cenário e fornecer informações sobre os requisitos de configuração.

> [!NOTE]
>
> Certifique-se de que permite uma [opção de netowrking](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) para a virtualização aninhada. Se não o fizer, resultará em erros de instalação do EFLOW. 

## <a name="deployment-on-local-vm"></a>Implantação em VM local
Esta é a abordagem de base para qualquer Windows VM que hospeda Azure IoT Edge para Linux no Windows. Para este caso, a virtualização aninhada tem de ser ativada antes de iniciar a implementação. Leia [o Hyper-V de execução numa máquina virtual com virtualização aninhada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) para obter mais informações sobre como configurar este cenário.

Se estiver a utilizar o Windows Server, [certifique-se de que instala a função Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Implantação em VMs Azure
Se optar por implementar em VMs Azure, note que não existe um interruptor externo por design. O Azure IoT Edge para Linux no Windows também não é compatível com um VM Azure que executa o Server SKU a menos que seja executado um script específico que traga um interruptor predefinido. Para obter mais informações sobre como criar um comutador predefinido, consulte a secção do Servidor do [Windows](#windows-server) abaixo. 

> [!NOTE]
>
> Quaisquer VMs Azure que supostamente acolhem EFLOW devem ser um VM que [suporte a virtualização aninhada](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Utilização limitada do interruptor externo
Em qualquer cenário em que o VM não possa obter um endereço IP através de um comutador externo, a funcionalidade de implementação utiliza automaticamente o interruptor interno por defeito para a implementação. Isto significa que a gestão do Azure IoT Edge para Linux VM só pode ser executada no próprio dispositivo-alvo (isto é, a ligação à Borda IoT Azure para o Linux VM através da extensão WAC PowerShell SSH só é possível na localidade).

## <a name="windows-server"></a>Windows Server
Para os utilizadores do Windows Server, note que o Azure IoT Edge para Linux no Windows não suporta automaticamente o interruptor predefinido.

* Consequências para o VM local: Certifique-se de que o VM EFLOW pode obter um endereço IP através do interruptor externo.

* Consequências para o Azure VM: Como não existe um interruptor externo nos VMs Azure, não é possível implementar o EFLOW antes de configurar um interruptor interno no servidor.

Não existe um interruptor predefinido no Server SKUs por padrão (independentemente de o Servidor SKU funcionar ou não num VM Azure). Ao ser implantado num VM Azure onde o interruptor externo não possa ser utilizado, o interruptor predefinido tem de ser configurado e configurado manualmente antes de Azure IoT Edge for Linux na implementação do Windows. A nossa funcionalidade de implementação cobre isto, uma vez que requer configuração IP para o interruptor interno, uma configuração NAT e instalação e configuração de um servidor DHCP. A nossa funcionalidade de implementação na pré-visualização pública afirma que não mexe com estas configurações para não prejudicar as configurações da rede em implementações produtivas.

* Informações relevantes sobre como configurar manualmente o interruptor padrão podem ser encontradas aqui: [Como permitir a virtualização aninhada em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Documentação sobre como configurar um servidor DHCP para este cenário pode ser encontrada aqui: [Implementar DHCP usando o Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)

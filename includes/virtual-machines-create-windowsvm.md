---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b0b5e817d5e39dd7800a1482d40c56db5f2be6ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109426"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo, clique em **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**.

    ![Navegue para as imagens de VM do Azure no portal](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. No Windows Server 2016 Datacenter, selecione o modelo de implementação Clássica. Clique em Criar.

    ![Captura de ecrã que mostra as imagens de VM do Azure disponíveis no portal](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Painel Básico

O painel Noções Básicas solicita as informações administrativas para a máquina virtual.

1. Introduza um **Nome** para a máquina virtual. Neste exemplo, _HeroVM_ é o nome da máquina virtual. O nome tem de ter entre 1-15 carateres e não pode conter carateres especiais.

2. Introduza um **Nome de utilizador** e uma **Palavra-passe** forte que são utilizados para criar uma conta local na VM. A conta local é utilizada para iniciar sessão e gerir a VM. No exemplo, _azureuser_ é o nome de utilizador.

   A palavra-passe tem de ter entre 8 e 123 carateres e cumprir três dos quatro requisitos de complexidade seguintes: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Saiba mais sobre os [requisitos de nomes de utilizador e palavras-passe](../articles/virtual-machines/windows/faq.md).

3. A **Subscrição** é opcional. Uma definição comum é "Pay As You Go".

4. Selecione um **Grupo de recursos** ou escreva o nome para um novo. No exemplo, _HeroVMRG_ é o nome do grupo de recursos.

5. Selecione uma **Localização** do datacenter do Azure onde pretende que a VM seja executada. No exemplo, **E.U.A. Leste** é a localização.

6. Quando tiver terminado, clique em **Seguinte** para continuar para o painel seguinte.

    ![Captura de ecrã que mostra as definições no painel Noções Básicas para a configuração de uma VM do Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Painel Tamanho

O painel Tamanho identifica os detalhes de configuração da VM e apresenta uma lista de várias opções que incluem o sistema operativo, o número de processadores, o tipo de armazenamento do disco e os custos de utilização mensal estimados.  

Escolha o **tamanho** de uma VM e, em seguida, clique em Selecionar para continuar. Neste exemplo, _DS1_\__V2 Standard_ é o tamanho da VM.

  ![Captura de ecrã do painel Tamanho que mostra os tamanhos da VM do Azure que pode selecionar](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Painel Definições

O painel Definições solicita opções de armazenamento e de rede. Pode aceitar as predefinições. O Azure cria entradas adequadas sempre que necessário.

Se tiver selecionado um tamanho da máquina virtual que o suporte, pode experimentar o Armazenamento Premium do Azure ao selecionar Premium (SSD) em Tipo de Disco.

Quando terminar de efetuar alterações, clique em **OK**.

## <a name="4-summary-blade"></a>4. Painel Resumo

O painel Resumo lista as definições especificadas nos painéis anteriores. Clique em **OK** quando estiver pronto para criar a imagem.

 ![O painel Resumo comunica as definições especificadas da máquina virtual](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Depois da máquina virtual ser criada, o portal lista a nova máquina virtual em **Todos os recursos** e apresenta um mosaico da máquina virtual no dashboard. A conta de armazenamento e o serviço cloud correspondentes também são criados e listados. A máquina virtual e o serviço cloud são iniciados automaticamente e o estado listado como **Em execução**.

 ![Configure o Agente da VM e os pontos finais da máquina virtual](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)

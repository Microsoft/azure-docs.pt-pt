---
title: Preparar VMs VMware que executem o Linux para a migração para o Azure com a migração do servidor de migrar do Azure | Documentos da Microsoft
description: Descreve como preparar uma VM do Linux para a migração para o Azure com a migração do servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811794"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Preparar as VMs do Linux VMware para migração para o Azure 

Este artigo descreve como preparar a executar o Linux quando pretender migrá-los para o Azure com VMs do VMware [do Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Migração de servidor migrar do Azure está atualmente em pré-visualização pública. Pode utilizar a versão de DG existente do Azure Migrate para descobrir e avaliar VMs para a migração, mas a migração real não é suportada na versão de DG existente.

Prepare máquinas do Linux da seguinte forma:

1. Instale serviços de integração do Linux de Hyper-V. As versões mais recentes de distribuições do Linux poderão ter isso instalado por predefinição).
2. Recrie a imagem de inicialização do Linux para que inclua controladores necessários do Hyper-V e para que a VM será arrancada no Azure (necessário para algumas distribuições).
3. Ative o registo da consola de série para resolução de problemas. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Atualize o ficheiro de mapa de dispositivo com o nome do dispositivo para associações de volume, ao utilizar identificadores de dispositivo persistente.
5. Atualize as entradas de fstab utilizar identificadores de volume persistente.
6. Remova quaisquer regras de udev reservar os nomes das interfaces com base no endereço de MAC etc.
7. Interfaces de rede de atualização para receber os endereços IP de DHCP.
8. Certifique-se de ssh está ativado. Verifique se o serviço de sshd está definido para ser iniciado automaticamente na reinicialização.
9. Certifique-se de que entrada ssh solicitações de conexão não são bloqueadas pela firewall do sistema operativo ou regras de tabela IP.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) sobre efetuar estas alterações em distribuições de Linux mais populares.

## <a name="sample-script"></a>Script de exemplo

Este script (preparar-para-azure.sh) fornece um exemplo para a preparação de máquinas do Linux. O script poderá não funcionar para todas as distribuições e ambientes, mas é um ponto de partida útil.

O script mostra como: 

- Voltar a gerar a imagem de inicialização do Linux com os controladores necessários, se necessário.
- Atualize as entradas de fstab utilizar identificadores de volume persistente.
- Redirecione os registos da consola para a porta serial.
- Ativar a consola de acesso de série do Azure
- Remover regras de rede de udev
- Injete uma execução de script de arranque que é executada quando a VM arranca. Ele verifica se a máquina está em execução no Azure. Se for, ele atualiza a configuração de rede na VM e define a primeira interface de ethernet para utilizar DHCP para adquirir um endereço IP.

### <a name="before-you-start"></a>Antes de começar

- O script de exemplo contém passos de exemplo. Ele não deve ser executado em sistemas de produção. Ela poderia danificar ou corromper a VM em que é executado.
- Recomendamos que executá-lo numa VM de teste. Antes de começar, fazer uma cópia de segurança VM ou um instantâneo, de modo a que pode restaurar a VM se for necessário. 
- O script funciona quando a VM está a executar um nessas distribuições de Linux:
    - Red Hat Enterprise Linux 6.5 +, 7.1 +
    - SO de cento 6.5 +, 7.1 +
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Executar o script

1. Copie o script para o Linux VM através de sftp ou um cliente de scp, como o FileZilla ou WinScp de teste.
2. SSH para a máquina do Linux com uma conta de administrador.
3. Navegue para o diretório de script.
4. Para tornar o script num arquivo executável, execute **sudo chmod 777 preparar-para-azure.sh**.
5. Execute o script com **./prepare-for-azure.sh**.

Eis como o script é executado:

![Script do Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux script ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux script](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux script ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux script](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux script ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux script](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux script ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux script](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar [mapeamento de dependências de máquina](how-to-create-group-machine-dependencies.md) para criar grupos de confiança elevada.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.

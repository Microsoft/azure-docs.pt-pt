---
title: Acerca das cópias de segurança de VMs do Azure
description: Saiba mais sobre a cópia de segurança de VM do Azure e tenha em atenção algumas práticas recomendadas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: f4a2fe4c9307f7e59ca94e47683356143546d090
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310748"
---
# <a name="about-azure-vm-backup"></a>Acerca das cópias de segurança de VMs do Azure

Este artigo descreve como o [serviço de cópia de segurança do Azure](backup-introduction-to-azure-backup.md) efetua uma cópia de segurança de VMs do Azure.

## <a name="backup-process"></a>Processo de cópia de segurança
Eis como cópia de segurança do Azure é concluída uma cópia de segurança para VMs do Azure.

1. Para as VMs do Azure que estão selecionadas para cópia de segurança, o serviço de cópia de segurança do Azure inicia uma tarefa de cópia de segurança em conformidade com o agendamento de cópia de segurança que especificar.
2. Durante a primeira cópia de segurança, uma extensão de cópia de segurança é instalada na VM, se estiver em execução.

    - Para VMs do Windows a _VMSnapshot_ extensão está instalada.
    - Para VMs do Linux a _VMSnapshotLinux_ extensão está instalada.
3. Para as VMs do Windows que estão a ser executado, cópia de segurança coordena com VSS para tirar um instantâneo consistente com a aplicação da VM.

    - Por predefinição a cópia de segurança permite cópias de segurança completas do VSS.
    - Se a cópia de segurança não consegue tirar um instantâneo consistente com a aplicação, em seguida, ele cria um instantâneo consistente com ficheiros do armazenamento subjacente (uma vez que não escritas das aplicações ocorrem enquanto a VM está parada).
4. Para a cópia de segurança do Linux VMs usa uma cópia de segurança consistente com ficheiros. Para instantâneos consistentes com a aplicação terá de personalizar os scripts de pré/pós.
5. Depois do instantâneo é tirado os dados são transferidos para o cofre.

    - Cópia de segurança está otimizada fazendo o backup de cada disco VM em paralelo.
    - Para cada disco a cópia de segurança, cópia de segurança do Azure lê os blocos no disco e identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior (o delta).
    - Depois do instantâneo é tirado, os dados são transferidos para o cofre.
    - Dados de instantâneos podem não ser imediatamente copiados para o cofre. Poderá demorar algumas horas em períodos de pico. Tempo total de cópia de segurança para uma VM será inferior a 24 horas para políticas de cópia de segurança diárias.

6. Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

![Arquitetura de cópia de segurança de máquina virtual do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="backing-up-encrypted-azure-vms"></a>Cópias de segurança encriptado VMs do Azure
Quando cria cópias de segurança de VMs do Azure com o Azure Backup, as VMs são encriptadas em descanso com o Storage Service Encryption (SSE). Além disso, cópia de segurança do Azure pode fazer uma cópia de segurança de VMs do Azure que são encriptados com a encriptação de disco do Azure (ADE).

**Encriptação** | **Detalhes** | **Suporte**
--- | --- | ---
**ADE** | ADE encripta os dados e SO de discos para VMs do Azure.<br/><br/> ADE integra-se com chaves de encriptação de disco BitLocker (BEK), guardados de forma segura num cofre de chaves como segredos, ou com chaves de encriptação de chave do Azure Key Vault (KEK). | O Azure Backup suporta a cópia de segurança de VMs do Azure geridos e não geridos encriptados com a BEK apenas, ou com a BEK juntamente com KEK.<br/><br/> Ambos os BEK e uma cópia de segurança e encriptados.<br/><br/> Uma vez que a KEK e BEK são uma cópia de segurança, se for necessário os utilizadores com permissões pode restaurar as chaves e segredos para o Cofre de chaves e recuperar a VM encriptada.<br/><br/> Não não possível ler encriptadas chaves e segredos por utilizadores não autorizados ou pelo Azure.
**SSE** | E o SSE, o armazenamento do Azure fornece encriptação em repouso, criptografando automaticamente os dados antes de os armazenar e descriptografa-a antes de obtenção. | O Azure Backup utiliza o SSE para encriptação de inativos de VMs do Azure.

- Cópia de segurança de VMs encriptadas com BitLocker encriptação de chave (BEK) apenas e BEK em conjunto com a chave de encriptação Key(KEK) é suportada, geridos e das VMs do Azure.
- A cópia de cópias de segurança (segredos) de BEK e KEK (chaves) são encriptados. Podem ser lidos e utilizados apenas quando restaurada para o Cofre de chaves por utilizadores autorizados. Nem os utilizadores não autorizados nem para o Azure pode ler ou utilize uma cópia chaves ou segredos de segurança.
- Uma vez que a BEK é também uma cópia de segurança, se a BEK é perdida, os utilizadores autorizados podem restaurar a BEK para o Cofre de chaves e recuperar a VM encriptada.
- Apenas os utilizadores com o nível certo de permissões podem criar cópias de segurança e restaurar VMs encriptadas, bem como as chaves e segredos.


## <a name="taking-snapshots"></a>Criar instantâneos
Instantâneos de cópia de segurança do Azure de acordo com a agenda de cópia de segurança.

- **VMs do Windows**: Para VMs do Windows, o serviço de cópia de segurança, as coordenadas com o serviço do Volume de cópia de sombra (VSS) para criar um instantâneo consistente com a aplicação dos discos da VM.
    - Por predefinição, o Azure Backup permite cópias de segurança completas do VSS. [Saiba mais](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Se pretender alterar a definição para que as cópias de segurança do Azure utiliza cópias de segurança de cópia VSS, defina a seguinte chave de registro num prompt de comando: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **VMs do Linux**: Se quiser criar instantâneos consistentes com a aplicação de VM do Linux, utilize o script prévio de Linux e estrutura para escrever seus próprios scripts personalizados para garantir a consistência do script posterior.
    -  O Azure Backup invoca apenas os scripts de pré/pós escritos por.
    - Se o script prévio e os pós-scripts de executam com êxito, o Azure Backup marca o ponto de recuperação consistentes com aplicações como. No entanto, está, por fim, responsável pela consistência de aplicação ao utilizar scripts personalizados.
    - [Saiba mais](backup-azure-linux-app-consistent.md) sobre a configuração de scripts.


### <a name="snapshot-consistency"></a>Consistência de instantâneo

A tabela seguinte explica os diferentes tipos de consistência.

**instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Application-consistent** | As cópias de segurança consistente com a aplicação capturam a memória conteúda e operações de e/s pendentes. Instantâneos consistentes com a aplicação utilizam o escritor VSS (ou script prévio/posterior para Linux) que assegurar a consistência dos dados da aplicação antes de ocorre uma cópia de segurança. | Ao recuperar com um instantâneo consistente com a aplicação, a VM arranca. Não existe danos em dados ou perda. Os aplicativos começam num estado consistente. | Windows: Todos os escritores VSS foi concluída com êxito<br/><br/> Linux: Pré/pós scripts são configurados e foi concluída com êxito
**Consistente do sistema de ficheiros** | Cópias de segurança de ficheiros fornecem cópias de segurança de ficheiros de disco através de um instantâneo de todos os ficheiros ao mesmo tempo.<br/><br/> | Ao recuperar com um instantâneo consistente com ficheiros, a VM arranca. Não existe danos em dados ou perda. As aplicações necessitam implementar seu próprio mecanismo de "correção de segurança" para se certificar de que os dados restaurados são consistentes. | Windows: Alguns dos escritores VSS falhou <br/><br/> Linux: A predefinição (se os scripts de pré/pós não são configurados ou falha)
**Crash-consistent** | Consistência de falhas sempre acontece quando uma VM do Azure é desligado no momento da cópia de segurança.  Apenas os dados que já existe no disco no momento da cópia de segurança são capturados e uma cópia de segurança.<br/><br/> Um ponto de recuperação consistentes com falhas não garante a consistência de dados para o sistema operativo ou a aplicação. | Há garantias de nenhum, mas normalmente as inicializações VM e forma com um disco de verificação para corrigir erros de danos. Quaisquer dados na memória ou de escrita que não foram transferida para o disco são perdidos. Aplicações implementam a verificação de seus próprios dados. Por exemplo, para uma aplicação de base de dados, se um registo de transações tem entradas que não estão na base de dados, o software de base de dados agrega até que os dados são consistentes. | VM está num Estado de encerramento


## <a name="backup-considerations"></a>Considerações sobre a cópia de segurança

**Consideração** | **Detalhes**
--- | ---
**Disco** | Operação de cópia de segurança otimiza fazendo o backup de cada um disco da VM em paralelo. Por exemplo, se uma VM tem quatro discos, o serviço tenta fazer cópias de segurança de todos os discos de quatro em paralelo. Para cada disco a cópia de segurança, cópia de segurança do Azure lê os blocos no disco e armazena apenas os dados alterados (cópia de segurança incremental).
**Agendamento** |  Para reduzir o tráfego de cópia de segurança, cópia de segurança de VMs diferentes em momentos diferentes do dia, sem qualquer sobrepõe-se. Cópia de segurança de VMs ao mesmo tempo faz com que o tráfego jams.
**A preparar cópias de segurança** | Considere o tempo de preparação de cópia de segurança, que inclui a instalar ou atualizar a extensão de cópia de segurança e acionar um instantâneo de acordo com a agenda de cópia de segurança.
**Transferência de dados** | Tempo necessário para o serviço de cópia de segurança calcular as alterações incrementais da cópia de segurança anterior.<br/><br/> Cópia de segurança incremental, determine as alterações nos computadores de serviço a soma de verificação do bloco. Se um bloco é alterado sua identificados para enviar para o cofre. O serviço de explorações em blocos identificados para tentar minimizar ainda mais dados a transferir. Depois de avaliar todos alterado bloqueado as alterações são transferidas para o cofre.<br/><br/> Pode haver um atraso entre o instantâneo e copiá-lo para o cofre.<br/><br/> Em horas de ponta pode demorar até oito horas para cópias de segurança do processo. O tempo de cópia de segurança para uma VM será inferior a 24 horas para a cópia de segurança diária.
**Cópia de segurança inicial** | Embora o tempo de cópia de segurança total de menos de 24 horas é válido para cópias de segurança incrementais, pode não ser para a primeira cópia de segurança. Tempo necessário dependerá de tamanho dos dados e quando é feita a cópia de segurança.
**Tempo de espera de fila** | Desde as cópia de segurança processos tarefas do serviço de várias contas de armazenamento de clientes ao mesmo tempo, dados de instantâneos podem não imediatamente ser copiados para o Cofre dos serviços de recuperação. Em períodos de pico de carga, pode demorar até oito horas antes das cópias de segurança são processadas. No entanto, o tempo de cópia de segurança de VM total é inferior a 24 horas para políticas de cópia de segurança diárias.


### <a name="backup-performance"></a>Desempenho de cópia de segurança
Esses cenários comuns podem afetar o tempo de cópia de segurança:

- Adicione um novo disco a uma VM protegida do Azure: Se uma VM está em cópia de segurança incremental e é adicionado um novo disco, a cópia de segurança pode durar mais de 24 horas devido a replicação inicial do novo disco, juntamente com a replicação de diferenças de discos existentes.
- Discos fragmentados: Operações de cópia de segurança são mais rápidas, quando as alterações de disco sejam colocadas. Se as alterações são distribuídas e fragmentadas num disco, a cópia de segurança será mais lenta.
- Alterações de disco: Se os discos protegidos em cópia de segurança incremental tiverem uma diária de alterações de mais de 200 GB, a cópia de segurança pode demorar muito tempo (mais de oito horas) para concluir.
- Modo de comparação (CC) de soma de verificação: O modo CC é comparativamente mais lento do que o modo otimizado utilizado pelo restaurar instantânea. Se já estiver a utilizar a restaurar instantânea e ter eliminado os instantâneos, em seguida, cópia de segurança muda para o modo CC, fazendo com que a operação de cópia de segurança ter mais de 24 horas (ou falha).

## <a name="restore-considerations"></a>Restaurar considerações

**Consideração** | **Detalhes**
--- | ---
**Restaurar a fila** | Processos de cópia de segurança do Azure restaurar trabalhos a partir de várias contas de armazenamento ao mesmo tempo, e pedidos de restauro são colocados numa fila.
**Restaurar cópia** | Durante o processo de restauro, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> Restaurar tempo depende de IOPS e débito da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não é carregada com outras aplicações escritas e leituras.


## <a name="best-practices"></a>Melhores práticas
Sugerimos que seguindo essas práticas ao configurar cópias de segurança VM:

- Considere a modificar o tempo de agenda de padrão definido numa política. Por exemplo, se o tempo predefinido é que a política é 12:00 AM, considere incrementando-o em minutos para que os recursos são utilizados de forma otimizada.
- Para a Premium VM a cópia de segurança no recurso de não - instantâneas RP aloca aproximadamente 50% de espaço de conta de armazenamento total. Serviço de cópia de segurança requer este espaço para copiar o instantâneo para a mesma conta de armazenamento e para transferi-los para o cofre.
- Para restaurar VMs a partir de um único cofre, é altamente recomendável usar diferentes [contas de armazenamento v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para garantir que a conta de armazenamento de destino não é limitada. Por exemplo, cada VM tem de ter conta de armazenamento diferente (se as 10 VMs são restauradas, em seguida, considere a utilização de 10 contas de armazenamento diferente).
- A restauração da camada de armazenamento de escalão 1 (instantâneo) será concluída em minutos (uma vez que é a mesma conta de armazenamento) em relação a camada de armazenamento de nível 2 (cofre) que pode demorar horas. Recomendamos que utilize [restaurar instantâneas](backup-instant-restore-capability.md) funcionalidade para restauros mais rápidos, caso em que os dados estão disponíveis na camada 1 (que tem os dados a ser restaurada do cofre, em seguida, irá demorar tempo).
- O limite do número de discos por conta de armazenamento é relativo ao peso os discos estão a ser acedidos por aplicações em execução na VM de IaaS. Certifique-se de que se vários discos estão alojados numa única conta de armazenamento. Como uma prática geral, se os discos de 5 a 10 ou mais estiverem presentes numa única conta de armazenamento, balancear a carga ao mover alguns discos para separar as contas de armazenamento.


## <a name="backup-costs"></a>Custos de cópia de segurança
VMs do Azure, uma cópia de segurança com o Azure Backup estão sujeitos aos [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).

- Faturação não iniciada até a conclusão da primeira cópia de segurança com êxito. Neste momento, começa a faturação para armazenamento e VMs protegidas.
- Faturação continua, desde que não há quaisquer dados de cópia de segurança armazenados num cofre para a VM. Se parar a proteção para uma VM, mas os dados de cópia de segurança para a VM existem num cofre, continua a faturação.
- Deixa de faturação de uma VM especificada apenas se a proteção é parada e todos os dados de cópia de segurança é eliminado.
- Quando parar a proteção e não há nenhuma tarefa de cópia de segurança Active Directory, o tamanho do último backup bem-sucedido de VM torna-se o tamanho de instância protegida utilizado para a fatura mensal.
- O cálculo de instâncias protegidas baseia-se sobre o *real* tamanho da VM, que é a soma de todos os dados na VM, excluindo o armazenamento temporário.
- Preço baseia-se em dados reais armazenados no disco de dados.
- Preços do backup das VMs não se baseia no tamanho máximo suportado para cada disco de dados ligado à VM.
- Da mesma forma, a fatura de armazenamento de cópia de segurança baseia-se na quantidade de dados que são armazenados em cópia de segurança do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, dê uma VM com tamanho A2 padrão com dois discos de dados adicionais com um tamanho máximo de 4 TB cada. A tabela seguinte fornece os dados reais armazenados em cada um destes discos:

**Disco** | **Tamanho máx.** | **Dados reais presentes**
--- | --- | ---
Disco do SO | 4095 GB | 17 GB
Disco local/temporária | 135 GB | 5 GB (não incluído para cópia de segurança)
Disco de dados 1 | 4095 GB | 30 GB
Disco de dados 2 | 4095 GB | 0 GB

- Neste caso, o tamanho real da VM é 17 GB + 30 GB + 0 GB = 47 GB.
- Este tamanho de instância protegida (47 GB) torna-se a base para a fatura mensal.
- Pelo contrário em conformidade à medida que aumenta a quantidade de dados na VM, o tamanho de instância protegida utilizado para alterações de cobrança.


## <a name="next-steps"></a>Passos Seguintes

Agora, [preparar](backup-azure-arm-vms-prepare.md) para cópia de segurança de VM do Azure.

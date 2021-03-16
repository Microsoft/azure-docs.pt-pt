---
title: Guia de migração para Azure Stack Edge Pro FPGA para dispositivo físico GPU
description: Este guia contém instruções para migrar cargas de trabalho de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 24d6528a105d593d1cb4c9c66d981c8787f85633
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573286"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrar cargas de trabalho de um Azure Stack Edge Pro FPGA para um GPU Azure Stack Edge Pro

Este artigo descreve como migrar cargas de trabalho e dados de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo GPU Azure Stack Edge Pro. O processo de migração começa com uma comparação entre os dois dispositivos, um plano de migração e uma revisão das considerações migratórias. O procedimento de migração dá passos detalhados terminando com verificação e limpeza do dispositivo.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Acerca da migração

Migração é o processo de movimentação de cargas de trabalho e dados de aplicação de um local de armazenamento para outro. Isto implica fazer uma cópia exata dos dados atuais de uma organização de um dispositivo de armazenamento para outro dispositivo de armazenamento - de preferência sem perturbar ou desativar aplicações ativas - e, em seguida, redirecionar toda a atividade de entrada/saída (I/O) para o novo dispositivo. 

Este guia de migração fornece uma passagem passo a passo dos passos necessários para migrar dados de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo GPU Azure Stack Edge Pro. Este documento destina-se a profissionais de tecnologias da informação (TI) e trabalhadores do conhecimento responsáveis pela operação, implantação e gestão de dispositivos Azure Stack Edge no datacenter.

Neste artigo, o dispositivo Azure Stack Edge Pro FPGA é referido como o dispositivo *de origem* e o dispositivo GPU Azure Stack Edge Pro é o dispositivo *alvo.* 

## <a name="comparison-summary"></a>Resumo da comparação

Esta secção fornece um resumo comparativo das capacidades entre o GPU Azure Stack Edge Pro vs. os dispositivos Azure Stack Edge Pro FPGA. O hardware tanto na fonte como no dispositivo-alvo é em grande parte idêntico; apenas o cartão de aceleração de hardware e a capacidade de armazenamento podem diferir.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Funcionalidade  | GPU Azure Stack Edge Pro (dispositivo alvo)  | Azure Stack Edge Pro FPGA (dispositivo de origem)|
|----------------|-----------------------|------------------------|
| Hardware       | Aceleração do hardware: 1 ou 2 GPUs Nvidia T4 <br> Computação, memória, interface de rede, unidade de alimentação e especificações do cabo de alimentação são idênticas ao dispositivo com a FPGA.  | Aceleração do hardware: Intel Arria 10 FPGA <br> Computação, memória, interface de rede, unidade de alimentação e especificações do cabo de alimentação são idênticas ao dispositivo com GPU.          |
| Armazenamento utilizável | 4.19 TB <br> Depois de reservar espaço para a resiliência da paridade e uso interno | 12.5 TB <br> Depois de reservar espaço para uso interno |
| Segurança       | Certificados |                                                     |
| Cargas de trabalho      | Cargas de trabalho IoT Edge <br> Cargas de trabalho de VM <br> Cargas de trabalho do Kubernetes| Cargas de trabalho IoT Edge |
| Preços        | [Preços](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Preços](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Plano de migração

Para criar o seu plano de migração, considere as seguintes informações:

- Desenvolver um horário de migração. 
- Quando migrar dados, poderá experimentar um tempo de inatividade. Recomendamos que marque a migração durante uma janela de manutenção de tempo de inatividade, uma vez que o processo é disruptivo. Irá configurar e restaurar as configurações neste tempo de inatividade, conforme descrito mais tarde neste documento.
- Compreenda a duração total do tempo de inatividade e comunique-a a todas as partes interessadas.
- Identifique os dados locais que precisam de ser migrados do dispositivo de origem. Como precaução, certifique-se de que todos os dados sobre o armazenamento existente têm uma cópia de segurança recente. 


## <a name="migration-considerations"></a>Migration considerations (Considerações sobre a migração) 

Antes de prosseguir com a migração, considere as seguintes informações: 

- Um dispositivo GPU Azure Stack Edge Pro não pode ser ativado contra um recurso Azure Stack Edge Pro FPGA. Deverá criar um novo recurso para o dispositivo GPU Azure Stack Edge Pro, conforme descrito na [ordem Create a azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Os modelos machine learning implantados no dispositivo de origem que utilizou o FPGA terão de ser alterados para o dispositivo-alvo com GPU. Para obter ajuda com os modelos, pode contactar o Microsoft Support. Os modelos personalizados implantados no dispositivo de origem que não utilizaram o FPGA (apenas CPU utilizado) devem funcionar como está no dispositivo-alvo (utilizando o CPU).
- Os módulos IoT Edge implantados no dispositivo de origem podem necessitar de alterações antes de os módulos poderem ser implantados com sucesso no dispositivo-alvo. 
- O dispositivo de origem suporta os protocolos NFS 3.0 e 4.1. O dispositivo-alvo suporta apenas o protocolo NFS 3.0.
- O dispositivo de origem suporta os protocolos SMB e NFS. O dispositivo-alvo suporta o armazenamento através do protocolo REST utilizando contas de armazenamento para além dos protocolos SMB e NFS para ações.
- O acesso à partilha no dispositivo de origem é através do endereço IP, enquanto que o acesso à partilha no dispositivo-alvo é através do nome do dispositivo.

## <a name="migration-steps-at-a-glance"></a>Passos de migração num ápice

Este quadro resume o fluxo global para a migração, descrevendo os passos necessários para a migração e o local onde tomar estas medidas.

| Nesta fase | Faça este passo| Neste dispositivo |
|---------------|-------------|----------------|
| Preparar dispositivo de origem*       | 1. Registar dados de configuração <br>2. Ressumo dados de partilha <br>3. Preparar cargas de trabalho IoT Edge| Dispositivo de origem  |
| Preparar dispositivo-alvo*       |1. Criar uma nova ordem <br>2. Configurar e ativar| Dispositivo-alvo  |
| Migrar dados       | 1. Migrar dados de ações <br>2. Reposicionar cargas de trabalho IoT Edge| Dispositivo-alvo  |
| Verificar os dados            |Verificar dados migrados |Dispositivo-alvo  |
| Limpar, voltar              |Apagar dados e devolver| Dispositivo de origem  |

**Os dispositivos de origem e alvo podem ser preparados em paralelo.*

## <a name="prepare-source-device"></a>Preparar dispositivo de origem

A preparação inclui que identifique as partilhas de nuvem Edge, as ações locais edge e os módulos IoT Edge implantados no dispositivo. 

### <a name="1-record-configuration-data"></a>1. Registar dados de configuração

Faça estes passos no seu dispositivo de origem através da UI local.

Grave os dados de configuração no dispositivo *de origem.* Utilize a [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md) para o ajudar a gravar a configuração do dispositivo. Durante a migração, utilizará esta informação de configuração para configurar o novo dispositivo alvo. 

### <a name="2-back-up-share-data"></a>2. Ressumo dados de partilha

Os dados do dispositivo podem ser de um dos seguintes tipos:

- Dados em ações de nuvem Edge
- Dados em ações locais

#### <a name="data-in-edge-cloud-shares"></a>Dados em ações de nuvem Edge

A nuvem edge partilha dados de nível do seu dispositivo para Azure. Faça estes passos no seu dispositivo *de origem* através do portal Azure. 

- Faça uma lista de todas as partilhas de nuvem Edge e utilizadores que tenha no dispositivo de origem.
- Faça uma lista de todos os horários de largura de banda que tem. Irá recriar estes horários de largura de banda no seu dispositivo-alvo.
- Dependendo da largura de banda disponível, configurar os horários de largura de banda no seu dispositivo para maximizar os dados hierárquicos para a nuvem. Isso minimiza os dados locais do dispositivo.
- Certifique-se de que as ações estão totalmente niveladas para a nuvem. O tiering pode ser confirmado verificando o estado da partilha no portal Azure.  

#### <a name="data-in-edge-local-shares"></a>Dados em ações locais edge

Os dados nas ações locais da Edge permanecem no dispositivo. Faça estes passos no seu dispositivo *de origem* através do portal Azure. 

- Faça uma lista das ações locais edge no dispositivo.
- Uma vez que vai fazer uma migração única dos dados, crie uma cópia dos dados de partilha locais edge para outro servidor no local. Pode utilizar ferramentas de cópia como `robocopy` (SMB) ou `rsync` (NFS) para copiar os dados. Opcionalmente, pode já ter implementado uma solução de proteção de dados de terceiros para fazer o back up dos dados nas suas ações locais. As seguintes soluções de terceiros são suportadas para utilização com dispositivos Azure Stack Edge Pro FPGA:

    | Software de terceiros           | Referência à solução                               |
    |--------------------------------|---------------------------------------------------------|
    | Coesidade                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para mais detalhes, contacte a Coesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para mais informações, contacte a Commvault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para mais detalhes, contacte a Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Preparar cargas de trabalho IoT Edge

- Se tiver implantado módulos IoT Edge e estiver a utilizar a aceleração FPGA, poderá ter de modificar os módulos antes de estes funcionarem no dispositivo GPU. Siga as instruções nos [módulos Modificar ioT Edge](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Preparar dispositivo-alvo

### <a name="1-create-new-order"></a>1. Criar nova ordem

É necessário criar uma nova ordem (e um novo recurso) para o seu *dispositivo-alvo.* O dispositivo-alvo deve ser ativado contra o recurso GPU e não contra o recurso FPGA.

Para esemcruitar uma encomenda, [crie um novo recurso Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) no portal Azure.


### <a name="2-set-up-activate"></a>2. Configurar, ativar

Tem de configurar e ativar o dispositivo *alvo* contra o novo recurso que criou anteriormente. 

Siga estes passos para configurar o dispositivo *alvo* através do portal Azure:

1. Recolha as informações necessárias na [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md). Pode utilizar as informações que guardou da configuração do dispositivo de origem. 
1. [Desembale,](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) [suporte de cremalheira](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cabo do seu dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Ligue-se à UI local do dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configure a rede utilizando um conjunto diferente de endereços IP (se utilizar IPs estáticos) do que os que utilizou para o seu dispositivo antigo. Veja como [configurar as definições de rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Atribua o mesmo nome do dispositivo que o seu antigo dispositivo e forneça um domínio DNS. Veja como configurar a [definição do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configure certificados no novo dispositivo. Veja como [configurar certificados.](azure-stack-edge-gpu-deploy-configure-certificates.md)
1. Obtenha a chave de ativação do portal Azure e ative o novo dispositivo. Ver como [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

Está agora pronto para restaurar os dados de partilha e implementar as cargas de trabalho que estava a executar no antigo dispositivo.

## <a name="migrate-data"></a>Migrar dados

Irá agora copiar dados do dispositivo de origem para as partilhas na nuvem Edge e para as ações locais edge no seu dispositivo *alvo.*

### <a name="1-from-edge-cloud-shares"></a>1. A partir de ações de nuvem edge

Siga estes passos para sincronizar os dados nas partilhas de nuvem Edge no seu dispositivo-alvo:

1. [Adicione ações](azure-stack-edge-j-series-manage-shares.md#add-a-share) correspondentes aos nomes de ações criados no dispositivo de origem. Quando criar as ações, certifique-se de que o **recipiente de bolhas Select** está definido para utilizar o dispositivo existente e, **em** seguida, selecione o recipiente que foi utilizado com o dispositivo anterior.
1. [Adicione utilizadores](azure-stack-edge-j-series-manage-users.md#add-a-user) que tenham tido acesso ao dispositivo anterior.
1. [Refresque os](azure-stack-edge-j-series-manage-shares.md#refresh-shares) dados de partilha do Azure. Refrescar a partilha irá retirar todos os dados da nuvem do recipiente existente para as ações.
1. Recrie os horários de largura de banda para serem associados às suas ações. Consulte [um calendário de largura de banda](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) para passos detalhados.


### <a name="2-from-edge-local-shares"></a>2. A partir de ações locais edge

Pode ter implementado uma solução de backup de terceiros para proteger os dados de partilhas locais para as suas cargas de trabalho IoT. Agora terá de restaurar esses dados.

Depois de configurar completamente o dispositivo de substituição, ative o dispositivo para armazenamento local. 

Siga estes passos para recuperar os dados das ações locais:

1. [Configure o cálculo do dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Adicione todas as ações locais no dispositivo alvo. Consulte os passos detalhados em [Adicionar uma parte local.](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)
1. O acesso às ações SMB no dispositivo de origem utilizará os endereços IP enquanto que no dispositivo-alvo, utilizará o nome do dispositivo. Consulte [a Ligação a uma participação da SMB no GPU Azure Stack Edge Pro .](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share) Para se ligar às ações da NFS no dispositivo-alvo, terá de utilizar os novos endereços IP associados ao dispositivo. Consulte [a Ligação a uma participação da NFS no GPU Azure Stack Edge Pro .](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share) 

    Se copiou os seus dados de partilha para um servidor intermédio sobre SMB ou NFS, pode copiar os dados do servidor intermédio para partilhar no dispositivo-alvo. Se a fonte e o dispositivo alvo estiverem *online,* também pode copiar os dados diretamente do dispositivo de origem.

    Se tiver usado software de terceiros para fazer o back up dos dados nas ações locais, terá de executar o procedimento de recuperação que é fornecido pela solução de proteção de dados de eleição. Consulte as referências na tabela seguinte.

    | Software de terceiros           | Referência à solução                               |
    |--------------------------------|---------------------------------------------------------|
    | Coesidade                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para mais detalhes, contacte a Coesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para mais informações, contacte a Commvault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para mais detalhes, contacte a Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para mais detalhes, contacte o Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Reposicionar cargas de trabalho IoT Edge

Uma vez preparados os módulos IoT Edge, terá de implementar cargas de trabalho IoT Edge no seu dispositivo-alvo. Se enfrentar erros na implementação de módulos IoT Edge, consulte:

- [Questões e resoluções comuns para Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [Erros de tempo de execução IoT Edge](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Verificar os dados

Após a migração, verifique se todos os dados migraram e as cargas de trabalho foram implantadas no dispositivo-alvo.

## <a name="erase-data-return"></a>Apagar dados, devolver

Após a conclusão da migração de dados, apoua os dados locais e devolva o dispositivo de origem. Siga os passos em [Devolver o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Passos seguintes

[Saiba como implementar cargas de trabalho IoT Edge no dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-compute-module-simple.md)

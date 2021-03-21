---
title: Descrição geral do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descreve o Azure Data Box Gateway, uma solução de armazenamento de aplicações virtuais que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 2adbefff2a0154268157c7d1735d83e70aa92ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563594"
---
# <a name="what-is-azure-data-box-gateway"></a>O que é Azure Data Box Gateway?

O Azure Data Box Gateway é uma solução de armazenamento que permite enviar facilmente dados para o Azure. Este artigo fornece uma descrição geral da solução Azure Data Box Gateway, os benefícios, as principais funções e os cenários nos quais pode implementar este dispositivo.

O Data Box Gateway é um dispositivo virtual com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor. O dispositivo virtual reside no local e escreve dados no mesmo com os protocolos NFS e SMB. O dispositivo transfere os dados para o blob de blocos do Azure, o blob de páginas ou Ficheiros do Azure.

## <a name="use-cases"></a>Casos de utilização

Pode tirar-se partido do Data Box Gateway para transferir dados para a cloud, como arquivo na cloud, recuperação após desastre ou, se for necessário, para processar os dados à escala da cloud. Eis os vários cenários em que o Data Box Gateway pode ser utilizado para transferência de dados.

- **Arquivo na cloud** - copie centenas de TBs de dados para o armazenamento do Azure com o Data Box Gateway de forma segura e eficiente. Os dados podem ser ingeridos uma vez ou de forma contínua para cenários de arquivo.

- **Ingestão contínua de dados** - Ingerir continuamente dados no dispositivo para copiar para a nuvem, independentemente do tamanho dos dados. À medida que os dados são escritos no dispositivo gateway, o dispositivo envia os dados para o Azure Storage.  

- **Transferência inicial a granel seguida de transferência incremental** - Utilize a Caixa de Dados para a transferência a granel num modo offline (semente inicial) e Gateway data box para transferências incrementais (feed em curso) através da rede.

Para mais informações, aceda aos [casos de utilização da Caixa de Dados Azure.](data-box-gateway-use-cases.md)

## <a name="benefits"></a>Benefícios

O Data Box Gateway possui os benefícios seguintes:

- **Transferência de dados fácil** - facilita mover dados de e para o armazenamento do Azure, sendo tão fácil como trabalhar numa partilha de rede local.  
- **Elevado desempenho** - elimina todos os problemas de transporte de dados de rede, com transferências de elevado desempenho de e para o Azure.
- **Acesso rápido e altas taxas de ingestão de dados durante** o horário comercial - Data Box Gateway tem uma cache local que você define como o tamanho da capacidade local quando o dispositivo virtual é a provisionado. O tamanho do disco de dados deve ser especificado de acordo com os [requisitos mínimos](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)do dispositivo virtual . A cache local proporciona os seguintes benefícios:
    - A cache local permite a ingestão de dados a uma taxa elevada. Quando uma elevada quantidade de dados é ingerida durante as horas de ponta, a cache pode reter os dados e carregá-lo para a nuvem.
    - A cache local permite o acesso de leitura rápida até um determinado limiar. Até que o dispositivo esteja 50-60% cheio, todas as leituras do dispositivo são acedidas a partir da cache tornando-as mais rápidas. Uma vez que o espaço utilizado no dispositivo ultrapasse este limiar, o dispositivo começa a remover os ficheiros locais.
 
- **Utilização de largura de banda limitada** - os dados podem ser escritos no Azure, mesmo quando a rede é limitada, para limitar a utilização durante o horário de pico.  

## <a name="key-capabilities"></a>Principais capacidades

O Data Box Gateway possui as seguintes funções:

|Funcionalidade |Description  |
|---------|---------|
|Velocidade     | Transferência de dados e largura de banda totalmente automatizadas e altamente otimizadas.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre as versões suportadas, aceda a [Requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).|
|Acesso a dados     | Uma vez que os dados enviados pelo dispositivo estão na nuvem, pode ser modificado adicionalmente acedendo diretamente às APIs da nuvem.|
|Acesso rápido     | Cache local no dispositivo para um acesso rápido aos ficheiros utilizados mais recentemente.|
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Resiliência     | Resiliência de rede incorporada        |


## <a name="specifications"></a>Especificações

O dispositivo virtual do Data Box Gateway tem as seguintes especificações:

| Especificações                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  |Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos|Disco do SO: 250 GB <br> Disco de dados: mínimo de 2 TB, com aprovisionamento dinâmico e tem de ser apoiado por SSDs|
| Interfaces de rede |1 ou mais interfaces de rede virtual|
| Protocolos de partilha de ficheiros nativos|SMB e NFS  |
| Segurança|Autenticação para desbloquear o acesso ao dispositivo e aos dados <br> Dados em trânsito encriptados com a encriptação AES de 256 bits|
| Gestão|IU da Web local - configuração inicial, diagnóstico e gestão de energia do dispositivos <br> Portal do Azure - gestão diária de dispositivos do Data Box Gateway       |

## <a name="components"></a>Componentes

A solução Data Box Gateway inclui o recurso Data Box Gateway, o dispositivo virtual Data Box Gateway e uma IU da Web local.

- **Dispositivo virtual Data Box Gateway** - um dispositivo com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor que permite enviar dados para o Azure.
    
- **Recurso Data Box Gateway** – um recurso no portal do Azure que permite gerir um dispositivo Data Box Gateway a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o recurso Data Box Gateway para visualizar e gerir dispositivos, partilhas, utilizadores e alertas. Para obter mais informações, consulte como [gerir utilizando o portal Azure.](data-box-gateway-manage-shares.md)

- **Data Box web uI local** - Utilize o UI web local para executar diagnósticos, desligar e reiniciar o dispositivo, gerar um pacote de suporte ou contactar o Microsoft Support para arquivar um pedido de serviço. Para obter mais informações, consulte como gerir utilizando a [UI web local.](data-box-gateway-manage-access-power-connectivity-mode.md)

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo Data Box Gateway, o recurso Azure e a conta de armazenamento alvo para a qual transfere dados nem todos têm de estar na mesma região.

- **Disponibilidade de recursos** - Para uma lista de todas as regiões onde está disponível o recurso Azure Data Box Gateway, aceda aos [produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox) O Data Box Gateway também pode ser implantado na Nuvem do Governo de Azure. Para mais informações, veja [o que é o Governo Azure?](../azure-government/documentation-government-welcome.md)

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure.

    As regiões nas quais as contas de armazenamento armazenam os dados do Data Box devem estar localizadas próximo de onde estiver o dispositivo, para um desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.


## <a name="next-steps"></a>Passos seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.

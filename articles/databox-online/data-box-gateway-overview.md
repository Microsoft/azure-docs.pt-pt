---
title: Descrição geral do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descreve o Azure Data Box Gateway, uma solução de armazenamento de aplicações virtuais que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e7a65a6b49544783ed3f40194e2338540819536b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400964"
---
# <a name="what-is-azure-data-box-gateway"></a>O que é o Gateway de caixa de dados do Azure?

O Azure Data Box Gateway é uma solução de armazenamento que permite enviar facilmente dados para o Azure. Este artigo fornece uma descrição geral da solução Azure Data Box Gateway, os benefícios, as principais funções e os cenários nos quais pode implementar este dispositivo.

O Data Box Gateway é um dispositivo virtual com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor. O dispositivo virtual reside no local e escreve dados no mesmo com os protocolos NFS e SMB. O dispositivo transfere os dados para o blob de blocos do Azure, o blob de páginas ou Ficheiros do Azure.

## <a name="use-cases"></a>Casos de utilização

Pode tirar-se partido do Data Box Gateway para transferir dados para a cloud, como arquivo na cloud, recuperação após desastre ou, se for necessário, para processar os dados à escala da cloud. Eis os vários cenários em que o Data Box Gateway pode ser utilizado para transferência de dados.

- **Arquivo na cloud** - copie centenas de TBs de dados para o armazenamento do Azure com o Data Box Gateway de forma segura e eficiente. Os dados podem ser ingeridos uma vez ou de forma contínua para cenários de arquivo.

- **Ingestão de dados contínua** -continuamente ingerir dados no dispositivo para copiar para a nuvem, independentemente do tamanho de dados. Como os dados são escritos para o dispositivo de gateway, o dispositivo carrega os dados ao armazenamento do Azure.  

- **Transferência em massa, seguida de transferência incremental de inicial** -caixa de dados de utilização para o volume transferir num modo offline (propagação inicial) e dados de caixa de Gateway para transferências incrementais (feed em curso) através da rede.

## <a name="benefits"></a>Benefícios

O Data Box Gateway possui os benefícios seguintes:

- **Transferência de dados fácil** - facilita mover dados de e para o armazenamento do Azure, sendo tão fácil como trabalhar numa partilha de rede local.  
- **Elevado desempenho** - elimina todos os problemas de transporte de dados de rede, com transferências de elevado desempenho de e para o Azure.
- **Rápida de acesso e as taxas de ingestão de elevados de dados durante o horário comercial** -dados de caixa de Gateway tem um cache local que definem como o tamanho de capacidade local quando o dispositivo virtual é aprovisionado. O tamanho do disco de dados deve ser especificado, de acordo a [requisitos mínimos do dispositivo virtual](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). A cache local fornece as seguintes vantagens:
    - A cache local permite a ingestão de dados a uma taxa elevada. Quando a elevada quantidade de dados é ingerida durante o horário de pico, o cache pode armazenar os dados e carregá-lo para a cloud.
    - A cache local permite o acesso de leitura rápido até um certo limite. Até que o dispositivo seja 50 a 60% completa, todas as leituras do dispositivo são acessadas da cache de torná-los o mais rapidamente. Assim que o espaço utilizado no dispositivo fica acima deste limiar, o dispositivo é iniciado remover ficheiros locais.
 
- **Utilização de largura de banda limitada** - os dados podem ser escritos no Azure, mesmo quando a rede é limitada, para limitar a utilização durante o horário de pico.  

## <a name="key-capabilities"></a>Principais capacidades

O Data Box Gateway possui as seguintes funções:

|Capacidade |Descrição  |
|---------|---------|
|Velocidade     | Transferência de dados e largura de banda totalmente automatizadas e altamente otimizadas.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre as versões suportadas, aceda a [Requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).|
|Acesso a dados     | Assim que os dados enviados pelo dispositivo estiverem na cloud, pode ser modificado de ainda mais ao aceder diretamente a APIs da nuvem.|
|Acesso rápido     | Cache local no dispositivo para um acesso rápido aos ficheiros utilizados mais recentemente.|
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Encriptação    | Suporte de BitLocker para encriptar localmente os dados e proteger a transferência de dados para a cloud através de *https*       |
|Resiliência     | Resiliência de rede incorporada        |


## <a name="specifications"></a>Especificações

O dispositivo virtual do Data Box Gateway tem as seguintes especificações:

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  |Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos|Disco do SO: 250 GB <br> Disco de dados: Mínimo de 2 TB, fino aprovisionado e tem de ser apoiado por SSDs|
| Interfaces de rede |1 ou mais interfaces de rede virtual|
| Protocolos de partilha de ficheiros nativos|SMB e NFS  |
| Segurança|Autenticação para desbloquear o acesso ao dispositivo e aos dados <br> Dados em trânsito encriptados com a encriptação AES de 256 bits|
| Gestão|IU da Web local - configuração inicial, diagnóstico e gestão de energia do dispositivos <br> Portal do Azure - gestão diária de dispositivos do Data Box Gateway       |

## <a name="components"></a>Componentes

A solução Data Box Gateway inclui o recurso Data Box Gateway, o dispositivo virtual Data Box Gateway e uma IU da Web local.

- **Dispositivo virtual Data Box Gateway** - um dispositivo com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor que permite enviar dados para o Azure.
    
- **Recurso Data Box Gateway** – um recurso no portal do Azure que permite gerir um dispositivo Data Box Gateway a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o recurso de Gateway de caixa de dados para ver e gerir o dispositivo, partilhas, os utilizadores e alertas. Para obter mais informações, consulte como [gerir através do portal do Azure](data-box-gateway-manage-shares.md).

- **Dados caixa da IU web local** – utilizar a IU da web local para executar diagnósticos, encerrar e reiniciar o dispositivo, gerar um pacote de suporte ou contacte Support da Microsoft para um pedido de serviço de ficheiros. Para obter mais informações, consulte como [gerir com a IU web local](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

Dispositivo físico de Gateway de caixa de dados, recursos do Azure e a conta de armazenamento de destino para a qual transferir dados nem todos precisam ser na mesma região.

- **Disponibilidade de recursos** -nesta versão, o recurso de Gateway de caixa de dados está disponível nas seguintes regiões que suportam a nuvem pública:
    - **Estados Unidos** -E.U.A. leste
    - **União Europeia** - Europa Ocidental
    - **Ásia-Pacífico** -Sudeste asiático

    Gateway de caixa de dados também podem ser implementada na Cloud do Azure Government. Para obter mais informações, consulte [o que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure.

    As regiões nas quais as contas de armazenamento armazenam os dados do Data Box devem estar localizadas próximo de onde estiver o dispositivo, para um desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.


## <a name="next-steps"></a>Passos Seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.


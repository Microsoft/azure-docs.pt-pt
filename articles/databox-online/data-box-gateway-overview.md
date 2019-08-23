---
title: Descrição geral do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descreve o Azure Data Box Gateway, uma solução de armazenamento de aplicações virtuais que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b8c6b4085f56bc12b67bf87177ba33b82c6a6db9
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900591"
---
# <a name="what-is-azure-data-box-gateway"></a>O que é Gateway do Azure Data Box?

O Azure Data Box Gateway é uma solução de armazenamento que permite enviar facilmente dados para o Azure. Este artigo fornece uma descrição geral da solução Azure Data Box Gateway, os benefícios, as principais funções e os cenários nos quais pode implementar este dispositivo.

O Data Box Gateway é um dispositivo virtual com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor. O dispositivo virtual reside no local e escreve dados no mesmo com os protocolos NFS e SMB. O dispositivo transfere os dados para o blob de blocos do Azure, o blob de páginas ou Ficheiros do Azure.

## <a name="use-cases"></a>Casos de utilização

Pode tirar-se partido do Data Box Gateway para transferir dados para a cloud, como arquivo na cloud, recuperação após desastre ou, se for necessário, para processar os dados à escala da cloud. Eis os vários cenários em que o Data Box Gateway pode ser utilizado para transferência de dados.

- **Arquivo na cloud** - copie centenas de TBs de dados para o armazenamento do Azure com o Data Box Gateway de forma segura e eficiente. Os dados podem ser ingeridos uma vez ou de forma contínua para cenários de arquivo.

- **Ingestão contínua de dados** – ingerir dados continuamente no dispositivo para copiar para a nuvem, independentemente do tamanho dos dados. À medida que os dados são gravados no dispositivo de gateway, o dispositivo carrega os dados no armazenamento do Azure.  

- A **transferência em massa inicial seguida por transferência incremental** -use data box para a transferência em massa em um modo offline (semente inicial) e gateway do data box para transferências incrementais (feed contínuo) pela rede.

Para obter mais informações, acesse [gateway do Azure data Box casos de uso](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Benefícios

O Data Box Gateway possui os benefícios seguintes:

- **Transferência de dados fácil** - facilita mover dados de e para o armazenamento do Azure, sendo tão fácil como trabalhar numa partilha de rede local.  
- **Elevado desempenho** - elimina todos os problemas de transporte de dados de rede, com transferências de elevado desempenho de e para o Azure.
- **Acesso rápido e altas taxas de ingestão de dados durante o horário comercial** -gateway do data Box tem um cache local que você define como o tamanho da capacidade local quando o dispositivo virtual é provisionado. O tamanho do disco de dados deve ser especificado de acordo com os [requisitos mínimos do dispositivo virtual](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). O cache local fornece os seguintes benefícios:
    - O cache local permite a ingestão de dados a uma taxa alta. Quando a quantidade alta de dados é ingerida durante o horário comercial de pico, o cache pode armazenar os dados e carregá-los na nuvem.
    - O cache local permite acesso de leitura rápido até certo limite. Até que o dispositivo seja 50-60% cheio, todas as leituras do dispositivo são acessadas do cache, tornando-as mais rápidas. Depois que o espaço usado no dispositivo ultrapassar esse limite, o dispositivo começará a remover os arquivos locais.
 
- **Utilização de largura de banda limitada** - os dados podem ser escritos no Azure, mesmo quando a rede é limitada, para limitar a utilização durante o horário de pico.  

## <a name="key-capabilities"></a>Principais capacidades

O Data Box Gateway possui as seguintes funções:

|Funcionalidade |Descrição  |
|---------|---------|
|Velocidade     | Transferência de dados e largura de banda totalmente automatizadas e altamente otimizadas.|
|Protocolos suportados     | Suporte para protocolos SMB e NFS standard de ingestão de dados. <br> Para obter mais informações sobre as versões suportadas, aceda a [Requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).|
|Acesso a dados     | Depois que os dados enviados pelo dispositivo estiverem na nuvem, eles poderão ser modificados ao acessar diretamente as APIs de nuvem.|
|Acesso rápido     | Cache local no dispositivo para um acesso rápido aos ficheiros utilizados mais recentemente.|
|Carregamento offline     | O modo desligado suporta cenários de carregamento offline.|
|Atualização de dados     | Capacidade de atualizar os ficheiros locais com a versão mais recente da cloud.|
|Resiliência     | Resiliência de rede incorporada        |


## <a name="specifications"></a>Especificações

O dispositivo virtual do Data Box Gateway tem as seguintes especificações:

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  |Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos|Disco do so: 250 GB <br> Disco de dados: mínimo de 2 TB, Thin provisionado e deve ser apoiado pelo SSDs|
| Interfaces de rede |1 ou mais interfaces de rede virtual|
| Protocolos de partilha de ficheiros nativos|SMB e NFS  |
| Segurança|Autenticação para desbloquear o acesso ao dispositivo e aos dados <br> Dados em trânsito encriptados com a encriptação AES de 256 bits|
| Gestão|IU da Web local - configuração inicial, diagnóstico e gestão de energia do dispositivos <br> Portal do Azure - gestão diária de dispositivos do Data Box Gateway       |

## <a name="components"></a>Componentes

A solução Data Box Gateway inclui o recurso Data Box Gateway, o dispositivo virtual Data Box Gateway e uma IU da Web local.

- **Dispositivo virtual Data Box Gateway** - um dispositivo com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor que permite enviar dados para o Azure.
    
- **Recurso Data Box Gateway** – um recurso no portal do Azure que permite gerir um dispositivo Data Box Gateway a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Use o recurso Gateway do Data Box para exibir e gerenciar dispositivos, compartilhamentos, usuários e alertas. Para obter mais informações, consulte como [gerenciar usando portal do Azure](data-box-gateway-manage-shares.md).

- **Data Box interface do usuário da Web local** -use a interface do usuário da Web local para executar o diagnóstico, desligar e reiniciar o dispositivo, gerar um pacote de suporte ou contatar suporte da Microsoft para arquivar uma solicitação de serviço. Para obter mais informações, consulte como [gerenciar usando a interface do usuário da Web local](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

Gateway do Data Box dispositivo físico, recurso do Azure e conta de armazenamento de destino para o qual você transfere os dados nem todos precisam estar na mesma região.

- **Disponibilidade de recursos** -para obter uma lista de todas as regiões em que o recurso Data Box Edge está disponível, vá para [produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). Gateway do Data Box também pode ser implantado na nuvem do Azure governamental. Para obter mais informações, consulte [o que é o Azure governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure.

    As regiões nas quais as contas de armazenamento armazenam os dados do Data Box devem estar localizadas próximo de onde estiver o dispositivo, para um desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em latências longas e desempenho mais lento.


## <a name="next-steps"></a>Passos seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.


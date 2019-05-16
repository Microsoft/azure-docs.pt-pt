---
title: Descrição geral do Microsoft Azure Data Box Heavy | Microsoft Docs em dados
description: Descreve o Azure Data Box, uma solução híbrida que permite transferir enormes quantidades de dados para o Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 780a50d9d470e8904e066459f97aa00d34ba1d3c
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603496"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>O que é o Azure Data Box Heavy? (Pré-visualização)

A solução híbrida Microsoft Azure Data Box permite enviar centenas de terabytes de dados para o Azure de forma rápida, económica e fiável. A transferência segura de dados é acelerada pelo envio de um dispositivo de armazenamento proprietário com capacidade de 1 PB de armazenamento através de uma transportadora. O dispositivo é fornecido numa caixa robusta para proteger os dados durante o transporte.

O Data Box Heavy está atualmente em pré-visualização. Pode inscrever-se para pedir um dispositivo através do portal do Azure. Quando receber o dispositivo no datacenter, pode configurá-lo com a IU da Web local. Copie os dados dos seus servidores para o dispositivo e envie-o para o Azure. No datacenter do Azure, os seus dados são carregados automaticamente do dispositivo para o Azure. O serviço Data Box faz o acompanhamento de todo o processo ponto a ponto no portal do Azure.


> [!IMPORTANT]
> - O Data Box Heavy está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implementar esta solução. 
> - Para pedir um dispositivo, inscreva-se no [Portal de pré-visualização](https://aka.ms/).
> - Durante a pré-visualização, o Data Box Heavy pode ser enviado aos clientes nos EUA e na União Europeia. Para obter mais informações, aceda a [Disponibilidade de região](#region-availability).

## <a name="use-cases"></a>Casos de utilização

O Data Box Heavy é perfeitamente adequado para transferir tamanhos de dados superiores a 500 TB em cenários com conectividade limitada ou sem conectividade de rede. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas. Eis os vários cenários em que o Data Box Heavy pode ser utilizado para transferência de dados.

 - **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure. 
     - Mover uma biblioteca de multimédia de bandas offline para o Azure, para criar uma biblioteca de multimédia online.
     - Migrar o farm de VMs, o SQL server e aplicações para o Azure
     - Mover dados históricos para o Azure, para uma análise aprofundada e criação de relatórios com o HDInsight

 - **Transferência em massa inicial** - quando é efetuada uma transferência em massa inicial com o Data Box Heavy (seed) seguida de transferências incrementais através da rede. 
     - Por exemplo, os parceiros de soluções de cópia de segurança, como o Commvault e o Data Box Heavy, são utilizados para mover grandes cópias de segurança iniciais do histórico para o Azure. Depois de concluído, os dados incrementais são transferidos através da rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.      

## <a name="benefits"></a>Benefícios

O Data Box Heavy foi concebido para mover enormes quantidades de dados para o Azure sem afetar a rede. A solução possui os benefícios seguintes:

- **Velocidade** - o Data Box Heavy utiliza interfaces de rede do alto desempenho de 40 Gbps.

- **Proteger** - o Data Box Heavy tem proteções de segurança incorporadas para o dispositivo, os dados e o serviço.
    - O dispositivo é fornecido numa caixa robusta protegida por parafusos resistente a adulterações e autocolantes invioláveis. 
    - Os dados no dispositivo são sempre protegidos com uma encriptação AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma palavra-passe fornecida no portal do Azure.
    - O serviço está protegido pelos recursos de segurança do Azure.
    - Assim que os dados forem carregados para o Azure, os discos do dispositivo são apagados de acordo com as normas 800-88r1 do NIST.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** - um dispositivo físico com um exterior robusto que armazena dados de forma segura. Este dispositivo tem uma capacidade de armazenamento utilizável de 800 TB. 

    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerir um dispositivo Data Box Heavy a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o serviço Data Box para a administração diária do seu dispositivo Data Box Heavy. As tarefas do serviço incluem criar e gerir pedidos, ver e gerir alertas, e gerir partilhas.  

* **Interface de utilizador da Web local** – uma interface de utilizador baseada na Web, que serve para configurar o dispositivo para que possa ligar à rede local e, em seguida, registar o dispositivo no serviço Data Box. Utilize também a IU da Web local para encerrar e reiniciar o dispositivo, ver registos de cópias e entrar em contacto com o Suporte da Microsoft para fazer um pedido de serviço.


## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal do Azure, forneça as informações de envio e a conta de armazenamento do Azure de destino para os seus dados. Se o dispositivo estiver disponível, o Azure prepara-o e envia-o com um ID de controlo de envio.

2. **Receber** – Assim que o dispositivo for entregue, instale os cabos especificados de alimentação e rede do dispositivo. Ative e ligue ao dispositivo. Configure a rede de dispositivo e monte partilhas no computador anfitrião a partir do qual pretende copiar os dados.

3. **Copiar dados** - copie dados para partilhas do Data Box Heavy.

4. **Devolver** - prepare, desative e envie o dispositivo novamente para o datacenter do Azure.

5. **Carregar** - os dados são copiados automaticamente do dispositivo para o Azure. O dispositivo é apagado em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Ao longo deste processo, será notificado por e-mail sobre todas as alterações de estado. 

## <a name="region-availability"></a>Disponibilidade regional

Dados de caixa pesada pode transferir os dados com base na região em que o serviço é implementado, país/região ao qual o dispositivo é enviado e a conta de armazenamento do Azure onde transferir os dados de destino. 

- **Disponibilidade do serviço** - nesta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da cloud pública nos Estados Unidos - E.U.A. Centro-Oeste, E.U.A. Oeste2, E.U.A. oeste, E.U.A. Centro-Sul, E.U.A. Central, E.U.A. Centro-Norte, E.U.A. Leste e E.U.A. Leste2.
    - União Europeia - Europa Ocidental e Europa do Norte.
    - Reino Unido - Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul de França.

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure onde o serviço estiver disponível. 

## <a name="sign-up"></a>Aderir

O Data Box Heavy está em pré-visualização e precisa de inscrever-se. Execute os seguintes passos para inscrever-se no Data Box Heavy:

1. Inicie sessão no Portal do Azure em: https://aka.ms/azuredatabox.
2. Clique em **+** para criar um novo recurso. Procure o **Azure Data Box**. Selecione o serviço **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Clique em **Criar**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Escolha a subscrição que pretende utilizar para a pré-visualização do Data Box Heavy. Selecione a região onde pretende implementar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Responder a perguntas sobre dados frequência de transferência de destino de período de tempo, residência país/região, o serviço do Azure para transferência de dados, largura de banda de rede e dados. Reveja a Privacidade e termos e selecione a caixa de verificação A Microsoft pode utilizar o seu endereço de e-mail para contactá-lo.

    <!--![The Data Box Heavy sign up 4]()-->

Assim que estiver inscrito e ativado para pré-visualização, pode encomendar um disco do Data Box Heavy.





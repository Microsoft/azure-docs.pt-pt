---
title: Descrição geral do Microsoft Azure Data Box Heavy | Microsoft Docs em dados
description: Descreve o Azure Data Box, uma solução híbrida que permite transferir enormes quantidades de dados para o Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437780"
---
# <a name="what-is-azure-data-box-heavy"></a>O que é o Azure Data Box Heavy?

A Azure Data Box Heavy permite-lhe enviar centenas de terabytes de dados para Azure de uma forma rápida, barata e fiável. Os dados são transferidos para o Azure enviando-lhe um dispositivo Data Box Heavy com capacidade de armazenamento de 1 PB, que preenche com os seus dados e envia de volta para a Microsoft. O aparelho tem um invólucro acidentado para proteger e proteger os seus dados durante o trânsito.

Assim que o dispositivo for recebido no seu datacenter, desemote-o utilizando a UI web local. Copie os dados dos seus servidores para o dispositivo e envie-o para o Azure. No datacenter Azure, os seus dados são enviados para a sua conta de Armazenamento Azure. Pode acompanhar todo o processo de ponta a ponta no portal Azure.


> [!IMPORTANT]
> - Para solicitar um dispositivo, inscreva-se no [portal Azure](https://portal.azure.com).


## <a name="use-cases"></a>Casos de utilização

Data Box Heavy é mais adequado para tamanhos de dados em centenas de terabytes, onde a conectividade da rede é insuficiente para carregar os dados para Azure. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas. Eis os vários cenários em que o Data Box Heavy pode ser utilizado para transferência de dados.

 - **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure.
     - Mover uma biblioteca de meios de comunicação de fitas offline para Azure para criar uma biblioteca de meios de comunicação online.
     - Migrar a sua quinta VM, servidor SQL e aplicações para o Azure.
     - Mover dados históricos para a Azure para análise aprofundada e reportar usando HDInsight.

 - **Transferência em massa inicial** - quando é efetuada uma transferência em massa inicial com o Data Box Heavy (seed) seguida de transferências incrementais através da rede.
     - Por exemplo, Data Box Heavy e um parceiro de soluções de backup são usados para mover uma cópia de segurança histórica inicial para Azure. Depois de concluído, os dados incrementais são transferidos através da rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.

## <a name="benefits"></a>Benefícios

Data Box Heavy é projetado para mover quantidades massivas de dados para Azure com pouco ou nenhum impacto na sua rede. A solução possui os benefícios seguintes:

- **Velocidade** - Data Box Heavy utiliza interfaces de rede de alto desempenho de 40 Gbps.

- **Segurança** - Data Box Heavy tem proteções de segurança incorporadas para o dispositivo, dados e o serviço.
    - O dispositivo é fornecido numa caixa robusta protegida por parafusos resistente a adulterações e autocolantes invioláveis.
    - Os dados no dispositivo são sempre protegidos com uma encriptação AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma palavra-passe fornecida no portal do Azure.
    - O serviço está protegido pelos recursos de segurança do Azure.
    - Uma vez que os seus dados são enviados para Azure, os discos do dispositivo são limpos, de acordo com as normas 800-88r1 do Instituto Nacional de Normas e Tecnologia (NIST).


## <a name="features-and-specifications"></a>Funcionalidades e especificações

O dispositivo Data Box Heavy tem as seguintes funcionalidades nesta versão.

| Especificações                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | ~ 500 libras. <br>Dispositivo sobre rodas de bloqueio para transporte|
| Dimensões                                              | Largura: 26 polegadas Altura: 28 polegadas comprimento: 48 polegadas |
| Espaço em rack                                              | Não pode ser montado em cremalheira|
| Cabos necessários                                         | 4 cabos elétricos 120 V / 10 A (NEMA 5-15) incluídos <br> O dispositivo suporta até 240 V de potência e tem recipientes de alimentação C-13 <br> Utilize cabos de rede compatíveis com [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Energia                                                    | 4 unidades de alimentação incorporadas (PSUs) partilhadas em ambos os nós do dispositivo <br> 1.200 watt típico power draw|
| Capacidade de armazenamento                                        | ~ 1-PB cru, 70 discos de 14 TB cada <br> Capacidade utilizável de 770 TB|
| Número de nós                                          | 2 nós independentes por dispositivo (500 TB cada) |
| Interfaces de rede por nó                             | 4 interfaces de rede por nó <br><br> MGMT, DATA3 <ul><li> 2 interfaces X 1-GbE </li><li> MGMT é para gestão e configuração inicial, não para utilizador configurável </li><li> Data3 é configurável pelo utilizador e protocolo de configuração do anfitrião dinâmico (DHCP) por padrão</li></ul>DATA1, data2 interfaces de dados <ul><li>2 interfaces X 40-GbE </li><li> Configurável do utilizador para DHCP (padrão) ou estático</li></ul>|


## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** - um dispositivo físico com um exterior robusto que armazena dados de forma segura. Este dispositivo tem uma capacidade de armazenamento utilizável de 770 TB.
    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerir um dispositivo Data Box Heavy a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o serviço Data Box para administrar o seu dispositivo Data Box Heavy. As tarefas do serviço incluem criar e gerir pedidos, ver e gerir alertas, e gerir partilhas.  

* **Interface de utilizador da Web local** – uma interface de utilizador baseada na Web, que serve para configurar o dispositivo para que possa ligar à rede local e, em seguida, registar o dispositivo no serviço Data Box. Utilize também a IU da Web local para encerrar e reiniciar o dispositivo, ver registos de cópias e entrar em contacto com o Suporte da Microsoft para fazer um pedido de serviço.


## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal do Azure, forneça as informações de envio e a conta de armazenamento do Azure de destino para os seus dados. Se o dispositivo estiver disponível, o Azure prepara-o e envia-o com um ID de controlo de envio.

2. **Receber** – Assim que o dispositivo for entregue, instale os cabos especificados de alimentação e rede do dispositivo. Ative e ligue ao dispositivo. Configure a rede de dispositivo e monte partilhas no computador anfitrião a partir do qual pretende copiar os dados.

3. **Copiar dados** - copie dados para partilhas do Data Box Heavy.

4. **Devolver** - prepare, desative e envie o dispositivo novamente para o datacenter do Azure.

5. **Carregar** - os dados são copiados automaticamente do dispositivo para o Azure. O dispositivo é apagado em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Ao longo deste processo, é notificado por e-mail sobre todas as alterações de estado.

## <a name="region-availability"></a>Disponibilidade de região

A Data Box Heavy pode transferir dados com base na região em que o serviço é implantado, país/região para onde o dispositivo é enviado, e a conta de armazenamento target Azure onde você transfere os dados.

- **Disponibilidade do serviço** - nesta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da cloud pública nos Estados Unidos - E.U.A. Centro-Oeste, E.U.A. Oeste2, E.U.A. oeste, E.U.A. Centro-Sul, E.U.A. Central, E.U.A. Centro-Norte, E.U.A. Leste e E.U.A. Leste2.
    - União Europeia - Europa Ocidental e Europa do Norte.
    - Reino Unido - Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul de França.

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure onde o serviço estiver disponível.

Para obter as informações mais atualizadas sobre a disponibilidade da região para data box heavy, vá aos [produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

## <a name="sign-up"></a>Inscrever-se

Tome os seguintes passos para se inscrever na Data Box Heavy:

1. [Inscreva-se no portal Azure](https://portal.azure.com).
2. Clique **+ Crie um recurso** para criar um novo recurso. Procure o **Azure Data Box**. Selecione o serviço **Azure Data Box**.
3. Clique em **Criar**.
4. Escolha a subscrição que pretende utilizar para Data Box Heavy. Selecione a região onde pretende implementar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.
5. Responda às questões relativas à residência de dados país/região, calendário, serviço Azure alvo para transferência de dados, largura de banda de rede e frequência de transferência de dados. Reveja a Privacidade e termos e selecione a caixa de verificação A Microsoft pode utilizar o seu endereço de e-mail para contactá-lo.

Uma vez inscrito, pode encomendar uma Caixa de Dados Pesada.

    

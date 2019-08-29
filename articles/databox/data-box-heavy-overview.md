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
ms.openlocfilehash: 362b7457538a16d389c3cc40fc44da19b073c0b0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142922"
---
# <a name="what-is-azure-data-box-heavy"></a>O que é o Azure Data Box Heavy?

Azure Data Box Heavy permite enviar centenas de terabytes de dados para o Azure de maneira rápida, barata e confiável. Os dados são transferidos para o Azure, fornecendo a você um dispositivo Data Box Heavy com capacidade de armazenamento de 1 PB, que você preenche com seus dados e envia de volta para a Microsoft. O dispositivo tem uma forte embalagem para proteger e proteger seus dados durante o trânsito.

Depois que o dispositivo for recebido em seu datacenter, configure-o usando a interface do usuário da Web local. Copie os dados dos seus servidores para o dispositivo e envie-o para o Azure. No datacenter do Azure, seus dados são carregados em suas contas de armazenamento do Azure. Você pode acompanhar todo o processo de ponta a ponta na portal do Azure.


> [!IMPORTANT]
> - Para solicitar um dispositivo, Inscreva-se no [portal do Azure](https://portal.azure.com).


## <a name="use-cases"></a>Casos de utilização

Data Box Heavy é mais adequado para tamanhos de dados em centenas de terabytes, em que a conectividade de rede é insuficiente para carregar os dados no Azure. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas. Eis os vários cenários em que o Data Box Heavy pode ser utilizado para transferência de dados.

 - **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure.
     - Mova uma biblioteca de mídia de fitas offline para o Azure para criar uma biblioteca de mídias online.
     - Migre seu farm de VM, SQL Server e aplicativos para o Azure.
     - Mova dados históricos para o Azure para análise detalhada e relatório usando o HDInsight.

 - **Transferência em massa inicial** - quando é efetuada uma transferência em massa inicial com o Data Box Heavy (seed) seguida de transferências incrementais através da rede.
     - Por exemplo, Data Box Heavy e um parceiro de soluções de backup são usados para mover o backup histórico inicial grande para o Azure. Depois de concluído, os dados incrementais são transferidos através da rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.

## <a name="benefits"></a>Benefícios

O Data Box Heavy foi projetado para mover grandes quantidades de dados para o Azure, com pouco ou nenhum impacto na sua rede. A solução possui os benefícios seguintes:

- **Velocidade** -data Box Heavy usa interfaces de rede de 40 Gbps de alto desempenho.

- **Security** -data Box Heavy tem proteções de segurança internas para o dispositivo, os dados e o serviço.
    - O dispositivo é fornecido numa caixa robusta protegida por parafusos resistente a adulterações e autocolantes invioláveis.
    - Os dados no dispositivo são sempre protegidos com uma encriptação AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma palavra-passe fornecida no portal do Azure.
    - O serviço está protegido pelos recursos de segurança do Azure.
    - Depois que os dados são carregados no Azure, os discos no dispositivo são apagados, de acordo com os padrões 88r1 NIST (National Institute of Standards and Technology) 800-Requery.


## <a name="features-and-specifications"></a>Funcionalidades e especificações

O dispositivo Data Box Heavy tem os seguintes recursos nesta versão.

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | ~ 500 lbs. <br>Dispositivo em rodas de bloqueio para transporte|
| Dimensões                                              | Largura: Altura de 26 polegadas: Tamanho de 28 polegadas: 48 polegadas |
| Espaço em rack                                              | Não pode ser montado em rack|
| Cabos necessários                                         | 4 com aterramento 120 V/10 um cabo de alimentação (NEMA 5-15) incluído <br> O dispositivo dá suporte a uma potência de até 240 V e tem Power receptáculo C-13 <br> Usar cabos de rede compatíveis com o [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Alimentar                                                    | 4 PSUs (unidades internas de fonte de alimentação) compartilhadas em ambos os nós de dispositivo <br> Power Draw típico de 1.200 watts|
| Capacidade de armazenamento                                        | ~ 1-PB brutos, 70 discos de 14 TB cada <br> capacidade utilizável de 770 TB|
| Número de nós                                          | 2 nós independentes por dispositivo (500 TB cada) |
| Interfaces de rede por nó                             | 4 interfaces de rede por nó <br><br> GERENCIAMENTO, DATA3 <ul><li> 2 X 1-interfaces GbE </li><li> O MGMT é para gerenciamento e configuração inicial, não configurável pelo usuário </li><li> O DATA3 é configurável pelo usuário e pelo protocolo DHCP (Dynamic Host Configuration Protocol) por padrão</li></ul>As interfaces de dados DADOS1, DATA2 <ul><li>interfaces 2 X 40-GbE </li><li> Configurável pelo usuário para DHCP (padrão) ou estático</li></ul>|


## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** - um dispositivo físico com um exterior robusto que armazena dados de forma segura. Este dispositivo tem uma capacidade de armazenamento utilizável de 770 TB.
    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerir um dispositivo Data Box Heavy a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Use o serviço de Data Box para administrar seu dispositivo de Data Box Heavy. As tarefas do serviço incluem criar e gerir pedidos, ver e gerir alertas, e gerir partilhas.  

* **Interface de utilizador da Web local** – uma interface de utilizador baseada na Web, que serve para configurar o dispositivo para que possa ligar à rede local e, em seguida, registar o dispositivo no serviço Data Box. Utilize também a IU da Web local para encerrar e reiniciar o dispositivo, ver registos de cópias e entrar em contacto com o Suporte da Microsoft para fazer um pedido de serviço.


## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal do Azure, forneça as informações de envio e a conta de armazenamento do Azure de destino para os seus dados. Se o dispositivo estiver disponível, o Azure prepara-o e envia-o com um ID de controlo de envio.

2. **Receber** – Assim que o dispositivo for entregue, instale os cabos especificados de alimentação e rede do dispositivo. Ative e ligue ao dispositivo. Configure a rede de dispositivo e monte partilhas no computador anfitrião a partir do qual pretende copiar os dados.

3. **Copiar dados** - copie dados para partilhas do Data Box Heavy.

4. **Devolver** - prepare, desative e envie o dispositivo novamente para o datacenter do Azure.

5. **Carregar** - os dados são copiados automaticamente do dispositivo para o Azure. O dispositivo é apagado em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Durante todo esse processo, você será notificado por email sobre todas as alterações de status.

## <a name="region-availability"></a>Disponibilidade de região

Data Box Heavy pode transferir dados com base na região em que o serviço é implantado, país/região no qual o dispositivo é enviado e a conta de armazenamento do Azure de destino onde você transfere os dados.

- **Disponibilidade do serviço** - nesta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da cloud pública nos Estados Unidos - E.U.A. Centro-Oeste, E.U.A. Oeste2, E.U.A. oeste, E.U.A. Centro-Sul, E.U.A. Central, E.U.A. Centro-Norte, E.U.A. Leste e E.U.A. Leste2.
    - União Europeia - Europa Ocidental e Europa do Norte.
    - Reino Unido - Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul de França.

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure onde o serviço estiver disponível.

Para obter as informações mais atualizadas sobre a disponibilidade de região para Data Box Heavy, acesse [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Aderir

Execute as seguintes etapas para se inscrever para Data Box Heavy:

1. Inicie sessão no Portal do Azure em: https://portal.azure.com.
2. Clique em **+ criar um recurso** para criar um novo recurso. Procure o **Azure Data Box**. Selecione o serviço **Azure Data Box**.
3. Clique em **Criar**.
4. Escolha a assinatura que você deseja usar para Data Box Heavy. Selecione a região onde pretende implementar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.
5. Responda às perguntas sobre país/região de residência de dados, hora-intervalo, serviço do Azure de destino para transferência de dados, largura de banda de rede e frequência de transferência de dados. Reveja a Privacidade e termos e selecione a caixa de verificação A Microsoft pode utilizar o seu endereço de e-mail para contactá-lo.

Depois de se inscrever, você pode solicitar um Data Box Heavy.

    

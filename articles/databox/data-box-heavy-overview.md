---
title: Descrição geral do Microsoft Azure Data Box Heavy | Microsoft Docs em dados
description: Descreve o Azure Data Box, uma solução híbrida que permite transferir enormes quantidades de dados para o Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 0f4657cdd71a104ca111f62a6e9757b5a33b46e8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592306"
---
# <a name="what-is-azure-data-box-heavy"></a>O que é o Azure Data Box Heavy?

Pesadas de caixa de dados do Azure permite-lhe enviar centenas de terabytes de dados para o Azure numa rápida, baixo custo e de forma fiável. Os dados são transferidos para o Azure ao enviar-lhe um dispositivo pesadas de caixa de dados com capacidade de armazenamento de 1 PB, preencher com os seus dados e enviar à Microsoft. O dispositivo tem uma rugged letras maiúsculas e minúsculas para proteger e proteger os seus dados durante o trânsito.

Depois do dispositivo é recebido no seu datacenter, configurá-lo com a IU web local. Copie os dados dos seus servidores para o dispositivo e envie-o para o Azure. No Centro de dados do Azure, os seus dados são carregados para suas contas de armazenamento do Azure. Pode controlar todo o processo de ponto-a-ponto no portal do Azure.


> [!IMPORTANT]
> - Para pedir um dispositivo, inscreva-se [portal do Azure](https://portal.azure.com).


## <a name="use-cases"></a>Casos de utilização

Dados de caixa pesado é mais adequado para tamanhos de dados a centenas de terabytes, em que a conectividade de rede é suficiente para carregar os dados para o Azure. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas. Eis os vários cenários em que o Data Box Heavy pode ser utilizado para transferência de dados.

 - **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure.
     - Mova uma biblioteca de suporte de dados de bandas offline para o Azure para criar uma biblioteca de suporte de dados online.
     - Migre do farm VM, o SQL server e a aplicativos para o Azure.
     - Mova dados históricos para o Azure para uma análise aprofundada e o relatório com o HDInsight.

 - **Transferência em massa inicial** - quando é efetuada uma transferência em massa inicial com o Data Box Heavy (seed) seguida de transferências incrementais através da rede.
     - Por exemplo, dados de caixa pesadas e um parceiro de soluções de cópia de segurança são utilizados para mover grandes histórica cópia de segurança inicial para o Azure. Depois de concluído, os dados incrementais são transferidos através da rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.

## <a name="benefits"></a>Benefícios

Dados de caixa pesada foi projetada para mover grandes quantidades de dados para o Azure com pouca ou nenhuma impacto na sua rede. A solução possui os benefícios seguintes:

- **Velocidade** -pesado de caixa de dados utiliza interfaces de rede de alto desempenho 40 Gbps.

- **Segurança** -pesado de caixa de dados tem proteções de segurança incorporadas para o dispositivo, dados e o serviço.
    - O dispositivo é fornecido numa caixa robusta protegida por parafusos resistente a adulterações e autocolantes invioláveis.
    - Os dados no dispositivo são sempre protegidos com uma encriptação AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma palavra-passe fornecida no portal do Azure.
    - O serviço está protegido pelos recursos de segurança do Azure.
    - Depois de seus dados são carregados para o Azure, os discos no dispositivo são apagados, de acordo com padrões de 800 88r1 National Institute of Standards and Technology (NIST).


## <a name="features-and-specifications"></a>Funcionalidades e especificações

O dispositivo de dados de caixa pesada tem as seguintes funcionalidades nesta versão.

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | ~ 500 lbs. <br>Dispositivo no bloqueio rodas para transporte|
| Dimensões                                              | Largura: Altura de 26 polegadas: 28 de polegadas de comprimento: polegadas 48 |
| Espaço em rack                                              | Não pode ser montado em bastidor|
| Cabos necessários                                         | 4 alicerçada às próprias 120 V / 10 A energia cabos (NEMA 5-15) incluídos <br> Dispositivo suporta power do até 240 V e tem receptacles de power C-13 <br> Utilize os cabos de rede compatíveis com [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Power                                                    | 4 energia incorporados fornecimento unidades (PSUs) partilhadas por nós de dispositivo <br> desenho de energia típico watt 1200|
| Capacidade de armazenamento                                        | ~ 1 PB não processado, 70 discos de 14 TB <br> Capacidade utilizável de 770 TB|
| Número de nós                                          | 2 nós independentes por dispositivo (500 TB cada) |
| Interfaces de rede por nó                             | 4 interfaces de rede por nó <br><br> MGMT, DATA3 <ul><li> Interfaces de 2 x 1 GbE </li><li> MGMT é para gestão e configuração inicial, o utilizador não configurável </li><li> DATA3 é configurável pelo utilizador e Dynamic Host Configuration Protocol (DHCP) por predefinição</li><li>As interfaces de rede de 1 GbE também podem ser configuradas como interfaces de 10 GbE</li></ul>Dados1, interfaces de dados de DATA2 <ul><li>Interfaces de 2 x 40 GbE </li><li> Utilizador configurável para DHCP (predefinição) ou estática</li></ul>|


## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** - um dispositivo físico com um exterior robusto que armazena dados de forma segura. Este dispositivo tem a capacidade de armazenamento utilizável de 770 TB.
    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerir um dispositivo Data Box Heavy a partir de uma interface Web à qual pode aceder a partir de localizações geográficas diferentes. Utilize o serviço do Data Box para administrar o seu dispositivo pesadas de caixa de dados. As tarefas do serviço incluem criar e gerir pedidos, ver e gerir alertas, e gerir partilhas.  

* **Interface de utilizador da Web local** – uma interface de utilizador baseada na Web, que serve para configurar o dispositivo para que possa ligar à rede local e, em seguida, registar o dispositivo no serviço Data Box. Utilize também a IU da Web local para encerrar e reiniciar o dispositivo, ver registos de cópias e entrar em contacto com o Suporte da Microsoft para fazer um pedido de serviço.


## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal do Azure, forneça as informações de envio e a conta de armazenamento do Azure de destino para os seus dados. Se o dispositivo estiver disponível, o Azure prepara-o e envia-o com um ID de controlo de envio.

2. **Receber** – Assim que o dispositivo for entregue, instale os cabos especificados de alimentação e rede do dispositivo. Ative e ligue ao dispositivo. Configure a rede de dispositivo e monte partilhas no computador anfitrião a partir do qual pretende copiar os dados.

3. **Copiar dados** - copie dados para partilhas do Data Box Heavy.

4. **Devolver** - prepare, desative e envie o dispositivo novamente para o datacenter do Azure.

5. **Carregar** - os dados são copiados automaticamente do dispositivo para o Azure. O dispositivo é apagado em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Ao longo deste processo, receberá uma notificação por e-mail sobre todas as alterações de estado.

## <a name="region-availability"></a>Disponibilidade de região

Dados de caixa pesada pode transferir os dados com base na região em que o serviço é implementado, país/região ao qual o dispositivo é enviado e a conta de armazenamento do Azure onde transferir os dados de destino.

- **Disponibilidade do serviço** - nesta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da cloud pública nos Estados Unidos - E.U.A. Centro-Oeste, E.U.A. Oeste2, E.U.A. oeste, E.U.A. Centro-Sul, E.U.A. Central, E.U.A. Centro-Norte, E.U.A. Leste e E.U.A. Leste2.
    - União Europeia - Europa Ocidental e Europa do Norte.
    - Reino Unido - Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul de França.

- **Contas de Armazenamento de Destino** - as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure onde o serviço estiver disponível.

Para obter as informações mais atualizadas sobre a disponibilidade de região para dados de caixa pesada, aceda a [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Inscrever-se

Siga os passos seguintes para se inscrever para dados de caixa pesado:

1. Inicie sessão no Portal do Azure em: https://portal.azure.com.
2. Clique em **+ criar um recurso** para criar um novo recurso. Procure o **Azure Data Box**. Selecione o serviço **Azure Data Box**.
3. Clique em **Criar**.
4. Escolha a subscrição que pretende utilizar para dados de caixa pesada. Selecione a região onde pretende implementar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.
5. Responder a perguntas sobre dados frequência de transferência de destino de período de tempo, residência país/região, o serviço do Azure para transferência de dados, largura de banda de rede e dados. Reveja a Privacidade e termos e selecione a caixa de verificação A Microsoft pode utilizar o seu endereço de e-mail para contactá-lo.

Assim que estiver inscrito no, é possível pedir uma pesada de caixa de dados.

    

---
title: Saiba mais sobre as funcionalidades nas Edições dos Serviços BizTalk | Microsoft Docs
description: 'Compare as capacidades das edições do BizTalk Services: Gratuita, programador, básico, Standard e Premium. MABS, WABS.'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: d16bf73c1d9871e4de8940e4ab9bbd7ed0f39384
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765633"
---
# <a name="biztalk-services-editions-chart"></a>Serviços BizTalk: Gráfico de edições

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os BizTalk Services do Azure oferecem várias edições. Utilize este artigo para determinar a edição que responde às suas necessidades empresariais e ao seu cenário.

## <a name="compare-the-editions"></a>Comparar as edições
**Gratuita (Pré-visualização)**

Pode criar e gerir Ligações Híbridas. Uma Ligação Híbrida permite ligar facilmente um site do Azure a um sistema no local, como o SQL Server.

**Programador**

Inclui Ligações Híbridas, processamento de mensagens EAI e EDI com um portal de gestão de parceiro comercial de utilização fácil e suporte para esquemas EDI comuns e processamentos EDI através de X12 e AS2. Pode criar cenários comuns de EAI, ligando serviços em nuvem a qualquer protocolo HTTP/S, REST, FTP, WCF e SFTP para ler e escrever mensagens.  Utilize a conectividade com os sistemas LOB no local com adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel e SQL Server prontos a utilizar. Utilize um ambiente centrado no desenvolvedor com ferramentas do Visual Studio para desenvolvimento e implementação fáceis. Limitado apenas a fins de desenvolvimento e teste sem nenhum Contrato de Nível de Serviço (SLA).

**Básica**

Inclui grande parte das capacidades da edição Programador com incrementos nas Ligações Híbridas, nas Pontes EAI, nos Contratos EDI e nas ligações do BizTalk Adapter Pack. Também oferece uma elevada disponibilidade e a opção de dimensionamento com um Contrato de Nível de Serviço (SLA).

**Standard**

Inclui todas as capacidades da edição Básica com incrementos nas Ligações Híbridas, nas Pontes EAI, nos Contratos EDI e nas ligações do BizTalk Adapter Pack. Também oferece uma elevada disponibilidade e a opção de dimensionamento com um Contrato de Nível de Serviço (SLA).

**Premium**

Inclui todas as capacidades da edição Standard com incrementos nas Ligações Híbridas, nas Pontes EAI, nos Contratos EDI e nas ligações do BizTalk Adapter Pack. Inclui ainda arquivamento, elevada disponibilidade e a opção de dimensionamento um Contrato de Nível de Serviço (SLA).

## <a name="editions-chart"></a>Gráfico de edições
A tabela que se segue lista as diferenças.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Gratuita (Pré-visualização)</th>
        <th>Programador</th>
        <th>Básica</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Preço inicial</strong></td>
<td colspan="5"><a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011">Preços dos Serviços BizTalk do Azure</a> <br/><br/> <a HREF="https://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de Preços do Azure</a></td>
</tr>
<tr>
<td><strong>Configuração mínima predefinida</strong></td>
<td>1 Unidade Gratuita</td>
<td>1 Unidade de Programador</td>
<td>1 Unidade Básica</td>
<td>1 Unidade Standard</td>
<td>1 Unidade Premium</td>
</tr>
<tr>
<td><strong>Dimensionamento</strong></td>
<td>Sem Dimensionamento</td>
<td>Sem Dimensionamento</td>
<td>Sim, em incrementos de 1 unidade Básica</td>
<td>Sim, em incrementos de 1 unidade Standard</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Aumento horizontal máximo permitido</strong></td>
<td>Sem Dimensionamento</td>
<td>Sem Dimensionamento</td>
<td>Até 8 Unidades</td>
<td>Até 8 Unidades</td>
<td>Até 8 Unidades</td>
</tr>
<tr>
<td><strong>Pontes EAI por unidade</strong></td>
<td>Não incluídos</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclui contratos TPM</td>
<td>Não incluído</td>
<td>Incluído. 10 contratos por unidade.</td>
<td>Incluído. 50 contratos por unidade.</td>
<td>Incluído. 250 contratos por unidade.</td>
<td>Incluído. 1000 contratos por unidade.</td>
</tr>
<tr>
<td><strong>Ligações Híbridas por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferência de Dados (GB) das Ligações Híbridas por Unidade</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Ligações do BizTalk Adapter Service a sistemas LOB no local</strong></td>
<td>Não incluídos</td>
<td>1 ligação</td>
<td>2 ligações</td>
<td>5 ligações</td>
<td>25 ligações</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos suportados:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Blob do Azure</li>
<li>APIs REST</li>
</ul>
</td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Elevada disponibilidade</strong>
<br/><br/>
Para o Contrato de Nível de Serviço (SLA), veja <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011">Preços dos BizTalk Services do Azure</a>.
</td>
<td>Não incluídos</td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Criar cópias de segurança e restauro</strong></td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Controlo</strong></td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Arquivo</strong><br/><br/>
Inclui a Não Rejeição de Receção (NRR) e a transferência de mensagens controladas</td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Não Incluído</td>
<td>Não Incluído</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Utilização de código personalizado</strong></td>
<td>Não incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
<tr>
<td><strong>Utilização de transformações, incluindo XSLT personalizado</strong></td>
<td>Não incluídos</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
<td>Incluída</td>
</tr>
</table>

> [!NOTE]
> Para resiliência contra falhas de hardware, a Elevada Disponibilidade implica ter várias VMs dentro de uma única Unidade BizTalk.
> 
> 

## <a name="faqs"></a>FAQs
#### <a name="what-is-a-biztalk-unit"></a>O que é uma unidade BizTalk?
Uma “unidade” é o nível atómico de uma implementação dos BizTalk Services do Azure. Cada edição é fornecida com uma unidade com capacidade de computação e memória diferente. Por exemplo, uma unidade Básica tem mais computação do que uma unidade Programador, uma unidade Standard tem mais computação do que uma unidade Básica, e assim sucessivamente. Quando dimensiona um BizTalk Service, está a dimensionar em termos de unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Qual é a diferença entre os BizTalk Services e a VM do BizTalk do Azure?
Os BizTalk Services fornecem uma arquitetura Plataforma-como-um-Serviço (PaaS) para criar soluções de integração em nuvem. Com o modelo PaaS, foca-se completamente na lógica da aplicação e confia toda a gestão da infraestrutura à Microsoft, por exemplo:

* Não tem de gerir ou aplicar patches em máquinas virtuais.
* A Microsoft garante a disponibilidade.
* Pode controlar o dimensionamento a pedido, solicitando mais ou menos capacidade através do Portal do Azure.

Em Virtual Machines do Azure, o BizTalk Server fornece uma arquitetura Infraestrutura-como-um-Serviço (IaaS). Pode criar máquinas virtuais e configurá-las exatamente como o seu ambiente no local, facilitando deste modo a execução de aplicações existentes na nuvem sem ter de alterar o código. Com a IaaS, o utilizador continua a ser responsável pela configuração das máquinas virtuais, assim como pela gestão das mesmas (por exemplo, instalação do software e aplicação de patches no SO) e pela arquitetura da aplicação para elevada disponibilidade.

Se pretender criar novas soluções de integração para minimizar o esforço de gestão da infraestrutura, utilize os BizTalk Services. Se pretender migrar rapidamente as suas soluções BizTalk existentes ou se estiver à procura de um ambiente a pedido para desenvolver e testar aplicações BizTalk Server, utilize o BizTalk Server numa Máquina Virtual do Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Qual é a diferença entre o BizTalk Adapter Service e as Ligações Híbridas?
O BizTalk Adapter Service é utilizado por um BizTalk Service do Azure. O BizTalk Adapter Service utiliza o BizTalk Adapter Pack para ligar a um sistema de Linha de Negócio (LOB) no local. Uma Ligação Híbrida permite ligar fácil e convenientemente aplicações do Azure, como a funcionalidade Aplicações Web no Serviço de Aplicações do Azure e nos Mobile Services do Azure, a um recurso no local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>O que significa “Transferência de Dados (GB) das Ligações Híbridas por unidade”? Significa por minuto/hora/dia/semana/mês? O que acontece quando se atinge o limite?
O custo das Ligações Híbridas por unidade depende da edição dos BizTalk Services. Resumindo, os custos dependem do volume de dados que transfere. Por exemplo, a transferência de 10 GB de dados por dia tem um custo inferior à transferência de 100 GB diários. Utilize a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/?scenario=full) dos BizTalk Services para determinar os custos específicos. Normalmente, os limites são impostos diariamente. Se exceder o limite, qualquer excesso será cobrado à taxa de $ 1 por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Quando crio um contrato nos BizTalk Services, o número de pontes aumenta de dois em dois em vez de um. Porquê?
Cada contrato inclui duas pontes diferentes: uma ponte de comunicação no lado do envio e uma ponte de comunicação no lado da receção.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>O que acontece quando atinjo o limite de quota do número de pontes ou contratos?
Deixará de poder implementar pontes novas ou criar novos contratos. Para implementar mais, terá de aumentar verticalmente mais unidades do serviço BizTalk ou atualizar para uma edição superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Como posso migrar de um escalão dos BizTalk Services para outro?
Não pode migrar nem “aumentar verticalmente” a edição Gratuita para outro escalão. Assim como também não pode fazer a cópia de segurança nem o restauro para outro escalão. Se precisar de outro escalão, crie um novo BizTalk Service com o novo escalão. Todos os artefactos criados com a edição Gratuita, incluindo as ligações híbridas, devem ser recriados no novo BizTalk Service. 

Para as restantes edições, utilize a cópia de segurança e o restauro para migrar os seus artefactos de um escalão para outro. Por exemplo, faça uma cópia de segurança dos seus artefactos no escalão Standard e, de seguida, restaure-os no escalão Premium. [Serviços BizTalk: Cópia de segurança e restaurar](biztalk-backup-restore.md) descreve os caminhos de migração suportados e lista os artefactos são uma cópia de segurança. Tenha em atenção que não são feitas cópias de segurança de Ligações Híbridas. Depois de criar as cópias de segurança e restaurar para um novo escalão, pode recriar as ligações híbridas.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>O BizTalk Adapter Service está incluído no serviço? Como recebo o software?
Sim, o BizTalk Adapter Service com o BizTalk Adapter Pack está incluído na [transferência](https://www.microsoft.com/download/details.aspx?id=39087) do SDK dos BizTalk Services do Azure.

## <a name="next-steps"></a>Passos Seguintes
Para criar os BizTalk Services no portal do Azure, aceda a [os serviços BizTalk: Aprovisionamento com o portal do Azure](biztalk-provision-services.md). Para começar a criar aplicações, veja [BizTalk Services do Azure](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionais
* [Serviços BizTalk: Aprovisionamento com o portal do Azure](biztalk-provision-services.md)<br/>
* [Serviços BizTalk: Gráfico de estado de aprovisionamento](biztalk-service-state-chart.md)<br/>
* [Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [Serviços BizTalk: Cópia de segurança e restauro](biztalk-backup-restore.md)<br/>
* [Serviços BizTalk: Throttling](biztalk-throttling-thresholds.md)<br/>
* [Serviços BizTalk: Nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>


---
title: Contratos e emendas do Azure EA
description: Este artigo explica como os contratos e as emendas do Azure EA afetam a utilização do Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 0b007e98f37246d17679761fd601ed76fd9fb875
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586233"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contratos e emendas do Azure EA

O artigo descreve como os contratos e as emendas do Azure EA podem afetar o acesso, a utilização e os pagamentos dos serviços do Azure.

## <a name="amendment-status"></a>Estado das emendas

Todas as emendas destinadas aos clientes do Azure precisam de passar pelo parceiro dos clientes ou pela equipa da conta Microsoft e de serem processadas pelo centro de operações regional. Se considerar que uma emenda não foi processada, verifique junto do seu parceiro, do assistente de software ou da equipa da conta Microsoft.

## <a name="enrollment-provisioning-status"></a>Estado do aprovisionamento da inscrição

A data de início de uma nova alocação monetária é definida pela data em que o centro de operações regional a processou. Dado que as encomendas de alocação monetária através do Azure EA Portal são processadas no fuso horário UTC, poderá sofrer algum atraso se a sua nota de encomenda de alocação monetária tiver sido processada numa região diferente. A data de início da cobertura na nota de encomenda em https://www.explore.ms mostra o início da alocação monetária. A data de início da cobertura aplica-se quando a alocação monetária aparece no Azure EA Portal.

## <a name="support-offer-not-provisioned"></a>Oferta de suporte não aprovisionada

Pode encomendar o suporte Standard ou Pro-Direct ao comprar SKUs específicos de suporte. A encomenda é semelhante a uma nota de encomenda do SKU Monetário do Azure EA. Por exemplo, 6QK-00001. Os números dos SKUs de suporte são W6T-00002 (Pro-Direct) e W6T-00003 (Standard). Verifique as ofertas de suporte atuais antes de comprar SKUs de suporte.

A inscrição tem de ter pelo menos uma conta ativa para que a oferta de suporte seja aprovisionada.

 A [Oferta de Plano de Suporte do Contrato Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/) do Azure está disponível para alguns clientes.

## <a name="enrollment-status"></a>Estado da inscrição

Uma inscrição tem um dos seguintes valores de estado. Cada estado determina como pode utilizar e aceder a uma inscrição. O estado da inscrição determina em que fase se encontra a inscrição. Indica se a inscrição precisa de ser ativada para a poder utilizar. Ou, se o período inicial expirou e está a ser cobrado por utilização excedida.

**Pendente** – o administrador da inscrição tem de iniciar sessão no Azure EA Portal. Depois de iniciar sessão, a inscrição muda para o estado **Ativo**.

**Ativo** – a inscrição está acessível e utilizável. Pode criar contas e subscrições no Azure EA Portal. A inscrição permanece ativa até à data limite do Contrato Enterprise.

**Período Alargado Indefinidamente** – o estado de período alargado indefinidamente ocorre depois de chegar à data limite do Contrato Enterprise. Antes de a inscrição EA atingir a data limite do Contrato Enterprise, o Administrador da Inscrição deve decidir se:

- Renova a inscrição ao adicionar uma Alocação Monetária adicional
- Transfere a inscrição existente para uma nova inscrição
- Migra para o Programa de Subscrição Online da Microsoft (MOSP)
- Confirma a desativação de todos os serviços associados à inscrição

**Expirado** – a inscrição EA expira quando atinge a data de fim do contrato Enterprise. O cliente EA é excluído do período alargado e todos os serviços são desativados.

Desde 1 de agosto de 2019 que não são aceites novos formulários de exclusão para clientes comerciais do Azure. Em vez disso, todas as inscrições entram no período alargado indefinidamente. Se quiser deixar de utilizar os serviços do Azure, termine a subscrição no [Azure EA Portal](https://portal.azure.com). Em alternativa, o seu parceiro pode submeter um pedido de cessação em https://www.explore.ms. Não existem alterações para os clientes com tipos de contratos governamentais.

**Transferido** – o estado Transferido aplica-se a inscrições cujas contas e serviços foram transferidos para uma nova inscrição. As inscrições não serão transferidas automaticamente se for gerado um novo número de inscrição durante a renovação. O número de inscrição anterior tem de ser incluído no pedido de renovação do cliente para que exista uma transferência automática.

## <a name="partner-markup"></a>Margem de Lucro do Parceiro

No Azure EA Portal, a Margem de Lucro do Parceiro permite obter melhores relatórios de custos para os clientes. O Azure EA Portal mostra a utilização e os preços configurados pelos parceiros para os clientes.

A margem de lucro permite que os administradores parceiros adicionem uma margem de lucro percentual aos Contratos Enterprise indiretos. A margem de lucro percentual aplica-se a todas as informações de serviços originais da Microsoft no Azure EA Portal, por exemplo: taxas dos medidores, alocações monetárias e encomendas. Depois de a margem de lucro ser publicada pelo parceiro, o cliente vê os custos do Azure no Azure EA Portal. Por exemplo, resumo da utilização, listas de preços e relatórios de utilização transferidos.

A partir de setembro de 2019, os parceiros podem aplicar a margem de lucro a qualquer momento durante um período. Não precisam de esperar até ao próximo aniversário para aplicar a margem de lucro.

A Microsoft não acederá nem utilizará a margem de lucro fornecida e preços associados para quaisquer fins, exceto se explicitamente autorizado pelo parceiro do canal.

### <a name="how-the-calculation-works"></a>Como funciona o cálculo

O LSP fornece um único valor percentual no EA Portal.  Todas as informações comerciais no portal serão aumentadas pela percentagem fornecida no LSP. Exemplo:

- O cliente assina um EA com uma alocação monetária de 100 000 USD.
- A taxa do medidor do Serviço A é de 10 USD/hora.
- O LSP define uma percentagem de margem de lucro de 10% no EA Portal.
- O exemplo abaixo mostra como o cliente verá as informações comerciais:
    - Saldo Monetário: 110 000 USD.
    - Taxa do medidor do Serviço A: 11 USD/Hora.
    - Informações de utilização/alojamento do serviço A quando utilizado durante 100 horas: 1100 USD.
    - Saldo monetário disponível para o cliente após a dedução do consumo do Serviço A: 108 900 USD.

### <a name="when-to-use-a-markup"></a>Quando utilizar a margem de lucro

Utilize esta funcionalidade caso tenha definido a mesma percentagem de margem de lucro para TODAS as transações comerciais no EA, isto é, se aplicar a margem de lucro às informações de alocação monetária, às taxas dos medidores, às informações de encomenda e etc.

Não utilize a funcionalidade de margem de lucro se:
- Utilizar taxas diferentes para a alocação monetária e as taxas dos medidores.
- Utilizar taxas diferentes para medidores diferentes.

Se estiver a utilizar taxas diferentes para medidores diferentes, recomendamos o desenvolvimento de uma solução personalizada com base na Chave de API, que pode ser fornecida pelo cliente para extrair os dados de consumo e fornecer relatórios.

### <a name="other-important-information"></a>Outras informações importantes

Esta funcionalidade destina-se a fornecer uma estimativa do custo do Azure ao cliente final. O LSP é responsável por todas as transações financeiras com o cliente ao abrigo do EA.

Confirme que analisa as informações comerciais (informações de saldo monetário, lista de preços e etc.) antes de publicar os preços com margem de lucro para o cliente final.

### <a name="how-to-add-a-price-markup"></a>Como adicionar a margem de lucro de um preço

**Passo 1: Adicionar a margem de lucro do preço**

1. No Enterprise Portal, clique em **Relatórios** no painel de navegação esquerdo.
1. Em _Resumo da Utilização_, clique nas palavras **Margem de lucro** a azul.
1. Introduza a percentagem da margem de lucro (entre -100 e 100) e clique em **Pré-visualização**.


**Passo 2: Analisar e validar**

Analise o preço da margem de lucro no _Resumo da Utilização_ para o período de alocação na vista de cliente. O preço da Microsoft ainda estará disponível na vista de parceiro. Pode alternar as vistas com o botão "pessoas" da margem de lucro do parceiro no canto superior direito.

1. Analise os preços na folha de preços.
1. Pode fazer alterações antes da publicação. Para tal, selecione **Editar** no separador _Ver Resumo da Utilização > Vista de Cliente_ .  
  Tanto os preços de serviço como os saldos de alocação terão a margem de lucro com as mesmas percentagens. Se tiver percentagens diferentes para o saldo monetário e as taxas dos medidores ou percentagens diferentes para serviços diferentes, não utilize esta funcionalidade.

**Passo 3: Publicar**

Após analisar e validar o preço, clique em **Publicar**.
  
Os preços com a margem de lucro estarão disponíveis para os administradores do Enterprise imediatamente após selecionar publicar. Não podem ser feitas edições às margens de lucro. Deve desativar a margem de lucro e começar a partir do Passo 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Que inscrições possuem uma margem de lucro ativada?

Para confirmar se uma inscrição tem uma margem de lucro publicada, clique em **Gerir** no painel de navegação esquerdo e clique no separador **Inscrição**. Selecione a caixa da inscrição a confirmar e veja o estado da margem de lucro em _Detalhes da Inscrição_. O estado atual da funcionalidade de margem de lucro para esse EA será apresentado como Desativado, Pré-visualização ou Publicado.

### <a name="how-can-the-customer-download-usage-estimates"></a>Como pode o cliente transferir as estimativas de utilização?

Quando a margem de lucro do parceiro for publicada, o cliente indireto terá acesso aos ficheiros .csv mensais de saldo e custo, assim como aos ficheiros .csv de detalhes da utilização. Os ficheiros de detalhes da utilização incluirão as taxas dos recursos e o custos total.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Como parceiro, como posso aplicar a margem de lucro a clientes do EA que anteriormente estavam com outro parceiro?
Os parceiros podem utilizar a funcionalidade de margem de lucro (no Azure EA) após ser processada uma Alteração do Parceiro de Canal; não precisam aguardar pelo próximo período de aniversário.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Alocação de recursos e pedir aumentos de quotas

**O sistema impõe as seguintes quotas predefinidas por subscrição:**

| **Recurso** | **Quota Predefinida** | **Comentários** |
| --- | --- | --- |
| Instâncias de Computação do Microsoft Azure | 20 instâncias de pequena computação simultâneas ou os equivalentes em outros tamanhos de instância de computação. | A seguinte tabela indica como calcular o número equivalente de pequenas instâncias:<ul><li> Extra Pequena – equivalente a 1 pequena instância </li><li> Pequena – equivalente a 1 pequena instância </li><li> Média – equivalente a 2 pequenas instâncias </li><li> Grande – equivalente a 4 pequenas instâncias </li><li> Extra Grande – equivalente a 8 pequenas instâncias </li> </ul>|
| VMs de Instâncias de Computação do Microsoft Azure v2 | EA: 350 Núcleos | VMs IaaS GA v2:<ul><li> Família A0\_A7 – 350 núcleos </li><li> Família B\_A0\_A4 – 350 núcleos </li><li> Família A8\_A9 – 350 núcleos </li><li> Família DF –350 núcleos</li><li> GF – 350 núcleos </li></ul>|
| Serviços Alojados do Microsoft Azure | 6 serviços alojados | Este limite de serviços alojados não pode ser aumentado para além de seis numa subscrição individual. Se precisar de serviços alojados adicionais, adicione subscrições adicionais. |
| Armazenamento do Microsoft Azure | 5 contas de armazenamento, cada uma com um tamanho máximo de 100 TB cada. | Pode aumentar o número de contas de armazenamento até 20 por subscrição. Se precisar de contas de armazenamento adicionais, adicione subscrições adicionais. |
| SQL Azure | 149 bases de dados de qualquer tipo (isto é, Web Edition ou Business Edition). |   |
| Controlo de Acesso | 50 Espaços de nomes por conta. 100 milhões transações de Controlo de Acesso por mês |   |
| Service Bus | 50 Espaços de nomes por conta. 40 ligações do Service Bus | Os clientes que comprarem ligações do Service Bus através de pacotes de ligação terão quotas iguais ao ponto médio entre o pacote de ligação que compraram e a quantidade do pacote de ligação mais elevado seguinte. Os clientes que escolherem um Pacote de 500 terão uma quota de 750. |

## <a name="resource-commitment"></a>Alocação de recursos

A Microsoft fornecerá serviços até, pelo menos, ao nível da utilização associada incluído na alocação mensal que comprou (a Alocação de Serviços), mas todos os outros aumentos nos níveis de utilização dos recursos do serviço (por exemplo, aumento do número de instâncias de comutação em execução ou o aumento da quantidade de armazenamento utilizado) estão sujeitos à disponibilidade destes recursos do serviço.

As quotas descritas acima não são Alocação de Serviços. Para determinar o número de pequenas instâncias de computação (ou equivalentes) simultâneas que a Microsoft fornecerá como parte da Alocação de Serviços, deverá dividir-se o número de horas das pequenas instâncias de computação compradas para um mês pelo número de horas no mês mais curto do ano (ou seja, fevereiro – 672 horas).

## <a name="requesting-a-quota-increase"></a>Pedir um aumento de quota

Pode pedir um aumento de quota a qualquer momento através da submissão de um [pedido online](https://g.microsoftonline.com/0WAEP00en/6). Para processar o pedido, forneça as seguintes informações:

- A conta Microsoft ou conta escolar ou profissional associada ao proprietário da conta da subscrição. Este é o endereço de e-mail utilizado para iniciar sessão no portal do Microsoft Azure para gerir as subscrições. Identifique também que esta conta está associada a uma inscrição EA.
- O(s) recurso(s) e a quantidade para a qual pretende que a quota aumente.
- O ID da Subscrição do Portal do Programador do Azure associado ao serviço.
  - Para obter informações sobre como obter o ID da subscrição, [contacte a assistência](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Plano de Compra Prévia de Computação (CPP) do Azure

O Plano de Compra Prévia de Computação (CPP, anteriormente conhecido como Promoção de Computação do Azure) do Azure é uma oferta que dá aos clientes um estado estável, cargas de trabalho previsíveis e uma oportunidade de obter computação com um desconto significativo ao comprar previamente a computação do Azure para 12 meses. Está disponível para o EA e está visível no EA Portal.

Se tiver encomendado uma SKU do CPP, esta destina-se a um tipo específico de computador, num datacenter específico. No estado em que se encontra atualmente esta funcionalidade, não fornecemos nenhuma lista de todas as compras CPP que um cliente realizou, pelo qual não existe nenhuma lista de créditos em espera; no entanto, quando configura os tipos de computadores comprados no datacenter onde foram comprados, verá a alocação mensal no relatório Resumo da Utilização no EA Portal.

O valor comprado previamente será apresentado como um crédito na coluna "Unidades Incluídas" e a sua utilização não resultará em custos de utilização até que as "Unidades Incluídas" sejam consumidas. Uma vez consumidas, a utilização adicional será cobrada às taxas negociadas na folha de preços do cliente EA normal.

A forma como os créditos são comprados baseia-se no número de computadores de uma classe por mês. Por exemplo: se tiver comprado 70 VMs A2 por ano no E.U.A. Leste 2, não veria isto no EA Portal do mesma forma como foi comprado.

Os computadores mensais são calculados como estando em execução 744 horas por mês. Ao converter esse valor em horas seria 70 vezes 744, ou seja 52 080 horas compradas de VMs A2 por mês. Dado que no Resumo da Utilização do EA Portal, as VMs A2 são indicadas em incrementos de 100 Horas, o que iria ver seria um crédito de 520,8 (52 080/100) "Unidades Incluídas" por mês e iria ver as unidades consumidas até à data no mês. Os custos de utilização devem aparecer como zero até que as unidades consumidas mensais ultrapassem as unidades mensais incluídas.

Diferentes tipos de computadores utilizam unidades de medida diferentes, por exemplo, as VMs D3 e D4 utilizam uma unidade de medida de 10 Horas no nosso relatório Resumo da Utilização. Assim, uma compra de 70, será apresentada como 5208 unidades incluídas por mês (52 080/10).

De uma forma geral, a fórmula abaixo fornecerá o número de VMs compradas com base nas unidades de medição:

<center><b> ("Quantidade Incluída" * "Unidades de Medida")/744 </b></center>

## <a name="plan-skus"></a>SKUs do Plano

As SKUs do Plano permitem comprar um conjunto de serviços integrados a uma taxa com desconto. As SKUs do Plano foram concebidas para se complementarem umas às outras através do conjunto e de ofertas integradas adicionais para uma maior economia.

Um exemplo disso seria a subscrição Operations Management Suite (OMS). O OMS oferece uma maneira simples de aceder a um conjunto completo de capacidades de gestão baseadas na cloud, incluindo análise, configuração, automação, segurança, cópia de segurança e recuperação após desastre. As subscrições OMS incluem direitos aos componentes do System Center para proporcionar uma solução completa para ambientes de cloud híbrida.

Os Administradores do Enterprise podem atribuir Proprietários da Conta para aprovisionar SKUs do Plano previamente compradas no Enterprise Portal com estes passos:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Ver a folha de preços para confirmar a quantidade incluída

1. Inicie sessão como Administrador do Enterprise.
1. Clique em **Relatórios** no painel de navegação esquerdo.
1. Clique no separador **Folha de Preços**.
1. Clique no ícone “Transferir” no canto superior direito.
1. Localize os números de peça das SKUS do Plano correspondentes ao filtro na coluna "Quantidade Incluída" e selecione os valores superiores a "0".

### <a name="provision-the-plan-skus"></a>Aprovisionar as SKUs do plano

Os administradores do Enterprise podem adicionar novos proprietários de conta, associar um proprietário de conta existente ou pedir aos proprietários de conta existentes para iniciarem sessão no Azure EA Portal para aprovisionar SKUs do Plano previamente compradas no Azure EA Portal com os passos abaixo.  

**Adicionar um novo Proprietário da Conta (concluído pelo administrador do EA):**

1. No Azure EA Portal, selecione **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Conta**.
1. Na página Conta, clique em **+ Adicionar Conta**.
1. Selecione um departamento ou deixe como não atribuído.
1. Selecione o tipo de autenticação pretendido.
1. Forneça um nome amigável que gostaria de utilizar para identificar esta conta no relatório.
1. Escreva o endereço de e-mail do proprietário da conta que quer associar a esta nova conta.
1. Confirme o endereço de e-mail que quer associar à nova conta.
1. Clique em **Adicionar**.
1. Pode adicionar outra conta ao clicar em **Adicionar Outra Conta** ou pode clicar no botão **Adicionar** no canto inferior direito da barra de ferramentas esquerda.
1. Agora, o proprietário da conta pode iniciar sessão para adicionar as subscrições relevantes para aprovisionar as SKUs do Plano.

**Associar um Proprietário de Conta Existente:**

1. No Enterprise Portal, clique em **Gerir**.
1. Clique no separador **Conta**.
1. Clique em **+Adicionar uma conta**. Introduza a conta Microsoft ou a conta escolar ou profissional associada à conta existente.
1. Confirme a conta Microsoft ou a conta escolar ou profissional associada à conta existente.
1. Forneça o nome que gostaria de utilizar para identificar esta conta no relatório.
1. Clique em **Adicionar**.
1. Pode adicionar uma conta adicional ao selecionar a opção **+ Adicionar uma Conta** novamente ou regressar à home page ao selecionar o botão **Admin**.  
1. Se clicar para ver a página Conta, a conta recentemente adicionada será apresentada com um estado “Pendente”. O estado será alterado para "Ativo" após o Proprietário da Conta iniciar sessão no EA Portal pela primeira vez.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Proprietários de conta novos/existentes para criar novas subscrições

**Passo 1: Iniciar sessão na conta**
1. No Azure EA Portal, selecione o separador **Gerir** e navegue até **Subscrição** no menu superior.
1. Verifique se tem sessão iniciada como proprietário desta conta.
1. Clique em **+ Adicionar Subscrição**.
1. Clique em **Comprar**.

Na primeira vez que adicionar uma subscrição a uma conta, terá de fornecer as suas informações de contacto. Ao adicionar subscrições posteriormente, as suas informações de contacto serão preenchidas automaticamente.

Na primeira vez que adicionar uma subscrição à conta, ser-lhe-á pedido que aceite o Contrato de Subscrição Online da Microsoft (MOSA) e um Plano de Taxas. Estas secções NÃO são Aplicáveis a Clientes do Contrato Enterprise, mas são atualmente necessárias para aprovisionar a subscrição. A Emenda da Inscrição do Contrato Microsoft Azure Enterprise prevalece sobre os itens acima e a sua relação contratual não será alterada. Selecione a caixa que indica que aceita os termos.

**Passo 2: Atualizar o nome da subscrição**

Todas as novas subscrições serão adicionadas com o nome de subscrição "Microsoft Azure Enterprise" predefinido. É importante atualizar o nome da subscrição para a diferenciar das outras subscrições na Inscrição do Enterprise e garantir que é reconhecível nos relatórios ao nível da empresa.

Clique em **Subscrições**, clique na subscrição que criou e, em seguida, clique em **Editar Detalhes da Subscrição**.

Atualize o nome da subscrição, o administrador de serviços e, em seguida, clique na marca de verificação. O nome da subscrição será apresentado nos relatórios e será também o nome do projeto associado à subscrição no portal de desenvolvimento.
As novas subscrições podem demorar até 24 horas para serem propagadas na lista de subscrições.

Apenas os proprietários da conta podem ver e gerir subscrições.

### <a name="troubleshooting"></a>Resolução de problemas

**Proprietário da conta apresenta no estado pendente**

Quando são adicionados novos Proprietários de Conta (AO) à inscrição pela primeira vez, serão sempre apresentados com o estado "pendente". Quando receber o e-mail de boas-vindas de ativação, o AO poderá iniciar sessão para ativar a conta. Esta ativação atualizará o estado da conta de "pendente" para "ativo".

**Utilizações que estão a ser faturadas após a compra de SKUs do Plano**

Este cenário ocorre quando o cliente implementou serviços ao abrigo do número de inscrição errado ou selecionou os serviços errados.

Para confirmar que está a implementar ao abrigo da inscrição correta, pode consultar as informações das unidades incluídas na folha de preços. Inicie sessão como Administrador do Enterprise, clique em **Relatórios** no painel de navegação esquerdo e selecione o separador **Folha de Preços**. Clique no ícone Transferir no canto superior direito, localize os números de peça das SKUs do Plano correspondentes ao filtro na coluna "Quantidade Incluída" e selecione os valores superiores a "0".

Confirme que o plano OMS é apresentado na folha de preços nas unidades incluídas. Se não existirem unidades incluídas para o plano OMS na inscrição, o plano OMS poderá estar noutra inscrição. Contacte o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Se as unidades incluídas para os serviços na folha de preços não corresponderem ao que implementou, por exemplo, Dados Analisados Premium de Informações Operacionais vs. Dados Analisados Standard de Informações Operacionais, significa que pode ter serviços implementados que não estão abrangidos pelo plano. Contacte o Suporte do Azure Enterprise Portal em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para podermos prestar-lhe mais apoio.

**Serviços de SKUs do Plano aprovisionados na inscrição errada**

Se possuir várias inscrições e tiver implementado serviços no número de inscrição errado, sem um plano OMS, contacte o Suporte do Azure Enterprise Portal através de em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Passos seguintes

- Para começar a utilizar o Azure EA Portal, veja [Get started with the Azure EA portal](ea-portal-get-started.md) (Introdução ao Azure EA Portal).
- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.

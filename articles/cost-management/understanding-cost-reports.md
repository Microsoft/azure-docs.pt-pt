---
title: Relatórios de gestão no Azure de custos do Cloudyn compreensão | Documentos da Microsoft
description: Este artigo ajuda-o a compreender a estrutura básica de relatórios gestão de custos Cloudyn e as funções.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 92ce35e88cc73c16e3d3670e07ff8b996192b825
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109819"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Cloudyn Noções básicas sobre relatórios de gestão de custos

Este artigo ajuda-o a compreender a estrutura básica de relatórios gestão de custos Cloudyn e as funções. A maioria dos relatórios do Cloudyn são intuitivos e tem uma aparência uniforme. Depois de ler este artigo, está pronto para utilizar todos os relatórios de gestão de custos. Muitas funcionalidades standard estão disponíveis ao longo de vários relatórios, permitindo que navegue até os relatórios com facilidade. Os relatórios são personalizáveis, e pode selecionar a partir de várias opções para calcule e exiba os resultados.

## <a name="report-fields-and-options"></a>Opções e campos de relatório

Aqui está uma olhada num exemplo do relatório de custo ao longo do tempo. A maioria dos relatórios do Cloudyn têm um esquema semelhante.

![Exemplo do relatório de custo ao longo do tempo com áreas numerados correspondente a descrições](./media/understanding-cost-reports/sample-report.png)

Cada área numerada na imagem anterior é descrita detalhadamente nas seguintes informações:

1. **Intervalo de datas**

    Utilize a lista de intervalo de datas para definir um intervalo de tempo de relatório através de uma configuração predefinida ou personalizada.
2. **Filtro guardado**

    Utilize a lista de filtros guardado para guardar a grupos atuais e os filtros que são aplicados ao relatório. Filtros guardados estão disponíveis em relatórios de custo e desempenho, incluindo:

      - Análise de custos
      - Alocação
      - Gestão de Ativos
      - Otimização

   Escreva um nome de filtro e clique em **guardar**.

3. **Etiquetas**

    Utilize a área de etiquetas ao grupo por categorias de marca. As etiquetas listadas no menu são departamento do Azure ou etiquetas de centro de custos ou forem etiquetas de entidade e uma subscrição de custo do Cloudyn. Selecione etiquetas para filtrar os resultados. Também pode escrever um nome de etiqueta (palavra-chave) para filtrar os resultados.

    ![Exemplo de uma lista de etiquetas para filtrar os resultados por](./media/understanding-cost-reports/select-options.png)

    Clique em **adicionar** para adicionar um novo filtro.

    ![Adicionar a caixa de filtro que mostra as opções e condições para filtrar por](./media/understanding-cost-reports/add-filter.png)

    Etiqueta de agrupamento ou filtragem não estão relacionadas aos recursos do Azure ou os sinalizadores de grupo de recursos.

    Agrupamento de marca de alocação de custos e a filtragem estão disponíveis no **grupos** opção de menu.

4. **Grupos em relatórios**

    Utilizar grupos na análise de custo relatórios para mostrar padrão, discriminados categorias de dados no seu relatório de faturação.  No entanto, os grupos na apresentação de relatórios de alocação de custos ver categorias com base na etiqueta. Etiqueta com base em categorias são definidas no modelo de alocação de custos e categorias de discriminados padrão de dados de faturação.

    ![Primeiro exemplo lista de etiquetas que pode agrupar por](./media/understanding-cost-reports/groups-tags01.png)

    ![Segundo exemplo lista de etiquetas que pode agrupar por](./media/understanding-cost-reports/groups-tags02.png)

    Nos relatórios de alocação de custos, podem incluir grupos de categorias com base na etiqueta de grupo:
      - Etiquetas
      - sinalizadores de grupo de recursos
      - Etiquetas de entidade de custos do Cloudyn
      - Categorias de etiqueta de subscrição para efeitos de alocação de custos

   Os exemplos podem incluir:
   - Centro de custos
   - Departamento
   - Aplicação
   - Ambiente
   - Código de custo

     Aqui está uma lista de grupos internos disponíveis nos relatórios:

     - **Tipo de custo**
     - Selecione um tipo de custo ou vários tipos de custo, ou todos. Tipos de custos incluem:
       - Tarifa única
       - Suporte
       - Custo de utilização
     - **Cliente**
       - Selecione um cliente específico, vários clientes, ou todos os clientes.
     - **Nome da conta**
       - O nome de conta ou a subscrição. No Azure, é o nome da subscrição do Azure.
     - **Conta não**
       - Selecione uma conta, várias contas ou todas as contas. No Azure, é o GUID da subscrição do Azure.
     - **Conta de principal**
       - Selecione a conta principal, várias contas ou selecione.
     - **Serviço**
       - Selecione um serviço, vários serviços, ou todos os serviços.
     - **Fornecedor**
       - O fornecedor de cloud em que estão associadas ativos e as despesas.
     - **Região**
       - Região onde está alojado o recurso.
     - **Zona de disponibilidade**
       - Localizações do AWS isolados numa região.
     - **Tipo de Recurso**
       - O tipo de recurso em uso.
     - **Subtipo**
       - Selecione o tipo de secundárias.
     - **Operação**
       - Selecione a operação ou **Mostrar tudo**.
     - **Modelo de preço**
       - Inicialmente todos
       - Não antes
       - Inicialmente parcial
       - A Pedido
       - Reserva
       - Lugar
     - **Tipo de custos**
       - Selecione o tipo de custo negativos ou positivos ou ambos.
     - **Inquilinos**
       - Se uma máquina está em execução como uma máquina dedicada.
     - **Tipo de utilização**
       - Tipo de utilização pode ser as tarifas únicas ou taxas recorrentes.

5. **Filtros**

    Utilize os filtros de seleção único ou múltipla para definir intervalos como valores selecionados. Para definir um filtro, clique em **adicionar** e, em seguida, selecione as categorias de filtro e valores.

6. **Modelo de custo**

    Utilize o modelo de custos para selecionar um modelo de custos que criou anteriormente com o Cost Allocation 360. Pode ter vários modelos de custos do Cloudyn, dependendo dos requisitos de alocação de custos. Algumas das suas equipas de organização podem ter custo requisitos de alocação que diferem das outras pessoas. Cada equipe pode ter seu próprio modelo de custos dedicado.

    Para obter informações sobre como criar uma definição de modelo de alocação de custos, veja [utilizar etiquetas personalizadas para alocar custos](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortização**

    Amortização de utilização nos relatórios de alocação de custos para ver a não utilização com base em taxas de serviço ou os custos de pagar uma única vez e distribuir os seus custos ao longo do tempo uniformemente durante o tempo de vida. Exemplos de tarifas únicas anteriores poderão incluir:
    - Tarifas de suporte anual
    - Taxas de componentes de segurança anual
    - Taxas de compra de instâncias reservadas
    - Alguns itens do Azure Marketplace.

   Em amortização, selecione **amortizado custo** ou **custo real**.

8. **Resolução**

    Utilize a resolução para selecionar a resolução de tempo dentro do intervalo de datas selecionado. O tempo de resolução determina como as unidades são apresentadas no relatório e podem ser:
    - Diariamente
    - Semanalmente
    - Custo
    - Trimestralmente
    - Anual

9. **Regras de alocação**

    Utilize regras de alocação para aplicar ou desativar o recálculo de custo de alocação de custos. Pode ativar ou desativar o recálculo de alocação de custo para dados de faturação. O recálculo aplica-se para as categorias selecionadas no relatório. Permite-lhe avaliar o impacto de recálculo de alocação de custos em relação a dados não processados de faturas.

10. **Não categorizado**

    Utilize não categorizado para incluir ou excluir os custos sem categoria no relatório.

11. **Mostrar/ocultar campos**

    A opção de Mostrar/ocultar não tem qualquer efeito nos relatórios.

12. **Formatos de apresentação**

    Utilize os formatos de apresentação para selecionar vários modos de exibição gráfico ou tabela.

    ![Símbolos de formatos de apresentação que pode selecionar](./media/understanding-cost-reports/display-formats.png)

13. **Cor multi**

    Utilize cores multi para definir a cor de gráficos no relatório.

14. **Ações**

    Utilize ações para salvar, exportar ou agendar o relatório.

15. **Política**

    Embora não mostrada, alguns relatórios incluem uma política de cálculo do custo previsto. Esses relatórios, o **consolidado** política mostra recomendações para todas as contas e subscrições na entidade atual, como inscrição no Microsoft Data ou AWS payer. O **autónomo** política mostra recomendações para uma conta ou subscrição, como se outras subscrições não existem. A política que selecionou varia com a estratégia de otimização utilizada pela sua organização. Custos projeções baseiam-se nos últimos 30 dias de utilização.

## <a name="save-and-schedule-reports"></a>Guardar e agendar relatórios

Depois de criar um relatório, pode guardá-lo para utilização futura. Relatórios guardados estão disponíveis no **ferramentas My** > **os meus relatórios**. Se efetuar alterações a um relatório existente e guardá-lo, o relatório é guardado como uma nova versão. Em alternativa, pode guardá-lo como um novo relatório.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Guardar um relatório para o portal do Cloudyn

Ao visualizar qualquer relatório, clique em **ações** e, em seguida, selecione **salvar em meus relatórios**. Nomeie o relatório e, em seguida, adicione um sua própria URL ou utilize o URL criado automaticamente. Pode opcionalmente **partilhar** o relatório publicamente com outras pessoas na sua organização ou pode partilhá-la à sua entidade. Se não partilham o relatório, ele continua sendo um pessoal de relatórios e que apenas pode ver. Guarde o relatório.


### <a name="save-a-report-to-cloud-provider-storage"></a>Guardar um relatório para o fornecedor de armazenamento na nuvem

Para guardar um relatório para o seu fornecedor de serviços cloud, tem de ter já configurado uma conta de armazenamento. Ao visualizar qualquer relatório, clique em **ações** e, em seguida, selecione **agendar relatório**. Nomeie o relatório e, em seguida, adicione um sua própria URL ou utilize o URL criado automaticamente. Selecione **guardar no armazenamento** e, em seguida, selecione a conta de armazenamento ou adicionar um novo. Introduza um prefixo que é anexado ao nome do ficheiro de relatório. Selecione um formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

### <a name="schedule-a-report"></a>Agendar um relatório

Pode executar relatórios em intervalos agendados e pode mandava-lo para um destinatário lista ou na cloud service fornecedor conta de armazenamento. Ao visualizar qualquer relatório, clique em **ações** e, em seguida, selecione **agendar relatório**. Pode enviar o relatório por correio eletrónico e guarde uma conta de armazenamento. Sob **agenda**, selecione o intervalo (diária, semanal ou mensal). Para semanais e mensais, selecione o dia ou datas para entregar e selecione a hora. Guarde o relatório agendado. Se selecionar o formato de relatório do Excel, o relatório é enviado como um anexo. Quando seleciona o formato do conteúdo de e-mail, os resultados do relatório que são apresentados no formato de gráfico são entregues como um gráfico.

### <a name="export-a-report-as-a-csv-file"></a>Exportar um relatório como um ficheiro CSV

Ao visualizar qualquer relatório, clique em **ações** e, em seguida, selecione **exportar todos os dados de relatório**. É apresentada uma janela de pop-up e é transferido um ficheiro CSV.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os relatórios que estão incluídos no Cloudyn na [relatórios de utilização Cloudyn](use-reports.md).
- Saiba mais sobre como utilizar relatórios para criar [dashboards](dashboards.md).

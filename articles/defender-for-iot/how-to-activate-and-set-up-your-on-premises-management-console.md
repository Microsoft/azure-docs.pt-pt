---
title: Ativar e configurar a sua consola de gestão no local
description: A ativação da consola de gestão garante que os sensores estão registados no Azure e envia informações para a consola de gestão no local, e que a consola de gestão no local realiza tarefas de gestão em sensores conectados.
ms.date: 4/6/2021
ms.topic: how-to
ms.openlocfilehash: db0d2a84feeb5bf52932842badda8c126994c05d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492159"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Ativar e configurar a sua consola de gestão no local 

Ativação e configuração da consola de gestão no local garantem que:

- Os dispositivos de rede que está a monitorizar através de sensores conectados estão registados numa conta Azure.

- Os sensores enviam informações para a consola de gestão no local.

- A consola de gestão no local realiza tarefas de gestão em sensores conectados.

- Instalou um certificado SSL.

## <a name="sign-in-for-the-first-time"></a>Sign in for the first time (Iniciar sessão pela primeira vez)

Para iniciar sinsução na consola de gestão:

1. Navegue para o endereço IP que recebeu para a consola de gestão no local durante a instalação do sistema.
 
1. Introduza o nome de utilizador e a palavra-passe que recebeu para a consola de gestão no local durante a instalação do sistema. 


Se esqueceu da sua palavra-passe, selecione a opção **Recuperar Palavra-Passe**  e consulte a [recuperação da palavra-passe](how-to-manage-the-on-premises-management-console.md#password-recovery) para obter instruções sobre como recuperar a sua palavra-passe.

## <a name="activate-the-on-premises-management-console"></a>Ativar a consola de gestão no local

Depois de iniciar sôms pela primeira vez, terá de ativar a consola de gestão no local, recebendo e carregando um ficheiro de ativação. 

Para ativar a consola de gestão no local:

1. Inscreva-se na consola de gestão no local.

1. Na notificação de alerta na parte superior do ecrã, selecione o link **Take Action.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="Selecione o link Take Action a partir do alerta na parte superior do ecrã.":::

1. No ecrã popup ativação, selecione o link **do portal Azure.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="Selecione a ligação do portal Azure a partir da mensagem pop-up.":::
 
1. Selecione uma subscrição para associar a consola de gestão no local e, em seguida, selecione o botão **de ficheiro de ativação da consola de gestão de descarregamento no local.** O ficheiro de ativação é descarregado.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Descarregue o ficheiro de ativação.":::

   Se ainda não entrou a bordo de uma subscrição, então [a bordo uma subscrição](how-to-manage-subscriptions.md#onboard-a-subscription).

1. Volte para o ecrã pop-up **ativação** e selecione **Choose File**.

1. Selecione o ficheiro descarregado.

Após a ativação inicial, o número de dispositivos monitorizados pode exceder o número de dispositivos comprometidos definidos durante o embarque. Isto ocorre se ligar mais sensores à consola de gestão. Se houver uma discrepância entre o número de dispositivos monitorizados e o número de dispositivos comprometidos, aparecerá um aviso na consola de gestão. Se isto acontecer, faça o upload de um novo ficheiro de ativação.

## <a name="set-up-a-certificate"></a>Configurar um certificado

Depois de instalar a consola de gestão, é gerado um certificado auto-assinado local. Este certificado é utilizado para aceder à consola. Depois de um administrador entrar pela primeira vez na consola de gestão, esse utilizador é solicitado a embarcar num certificado SSL/TLS. 

Estão disponíveis dois níveis de segurança:

- Satisfaça os requisitos específicos de certificado e encriptação solicitados pela sua organização, fazendo o upload do certificado assinado pela AC.
- Permitir validação entre a consola de gestão e os sensores ligados. A validação é avaliada com uma lista de revogação de certificados e a data de validade do certificado. *Se a validação falhar, a comunicação entre a consola de gestão e o sensor é interrompida e um erro de validação é apresentado na consola.* Esta opção é ativada por padrão após a instalação.  

A consola suporta os seguintes tipos de certificados:

- Infraestrutura chave privada e empresarial (PKI privado)

- Infraestruturas de chaves públicas (PKI público)

- Gerado localmente no aparelho (auto-assinado localmente) 

  > [!IMPORTANT]
  > Recomendamos que não use um certificado auto-assinado. O certificado não é seguro e deve ser utilizado apenas para ambientes de ensaio. O dono do certificado não pode ser validado, e a segurança do seu sistema não pode ser mantida. Nunca utilize esta opção para redes de produção.

Para fazer o upload de um certificado:

1. Quando for solicitado após a sinsind.

1. Faça o upload do CRT e dos ficheiros chave.

1. Introduza uma palavra-passe e carreme um ficheiro PEM, se necessário.

Poderá ter de atualizar o ecrã depois de ter enviado o certificado assinado pela AC.

Para desativar a validação entre a consola de gestão e os sensores ligados:

1. Selecione **Seguinte**.

1. Desligue o **alternador de validação** para todo o sistema .

Para obter informações sobre o upload de um novo certificado, ficheiros de certificados suportados e itens relacionados, consulte [Gerir a consola de gestão no local.](how-to-manage-the-on-premises-management-console.md)

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Ligue os sensores à consola de gestão no local

Deve garantir que os sensores enviam informações para a consola de gestão no local e que a consola de gestão no local pode realizar backups, gerir alertas e realizar outras atividades nos sensores. Para isso, utilize os seguintes procedimentos para verificar se escruisca uma ligação inicial entre os sensores e a consola de gestão no local.

Estão disponíveis duas opções para ligar o Azure Defender para sensores IoT à consola de gestão no local:

- Ligar a partir da consola sensor

- Conecte-se utilizando túneis

Depois de ligar, deve configurar um site com estes sensores.

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>Ligue os sensores à consola de gestão no local a partir da consola sensorial

Pode ligar os sensores à consola de gestão no local a partir da consola sensorial:

1. Na consola de gestão no local, selecione **Definições do Sistema**.

1. Copie a **cadeia de ligação de cópia**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="Copie o fio de ligação para o sensor.":::

1. No sensor, navegue para **definições do sistema** e selecione **Ligação à Consola de Gestão**:::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false":::

1. Cole a cadeia de ligação copiada da consola de gestão no local para o campo **de cordas Connection.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="Cole o fio de ligação copiado no campo de cordas de ligação.":::

1. Selecione **Ligar**.

### <a name="connect-sensors-by-using-tunneling"></a>Ligue os sensores utilizando túneis

Permitir uma ligação de túneis segura entre sensores organizacionais e a consola de gestão no local. Esta configuração contorna a interação com a firewall organizacional e, como resultado, reduz a superfície de ataque.

A utilização de túneis permite ligar-se à consola de gestão no local a partir do seu endereço IP e de uma única porta (ou seja, 9000) a qualquer sensor.

Para instalar um túnel na consola de gestão no local:

- Inscreva-se na consola de gestão no local e execute os seguintes comandos:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Para configurar o túnel no sensor:

1. Abra manualmente a porta TCP 9000 no sensor (network.properties). Se a porta não estiver aberta, o sensor rejeitará a ligação da consola de gestão no local.

2. Inscreva-se em cada sensor e execute os seguintes comandos:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Criar um site

O mapa padrão da empresa fornece uma visão geral dos seus dispositivos de acordo com vários níveis de localizações geográficas.

A visão dos seus dispositivos pode ser necessária onde a estrutura organizacional e as permissões do utilizador são complexas. Nestes casos, a configuração do site pode ser determinada por uma estrutura organizacional global, além da estrutura padrão do site ou da zona.

Para apoiar este ambiente, você precisa criar uma topologia de negócios global baseada nas unidades de negócio, regiões, locais e zonas da sua organização. Também é necessário definir permissões de acesso ao utilizador em torno destas entidades, utilizando grupos de acesso.

Os grupos de acesso permitem um melhor controlo sobre onde os utilizadores gerem e analisam dispositivos na plataforma Defender para IoT.

### <a name="how-it-works"></a>Como funciona

Você pode definir uma unidade de negócio, e uma região para cada site na sua organização. Em seguida, pode adicionar zonas, que são entidades lógicas que existem na sua rede. 

Deve atribuir pelo menos um sensor por zona. O modelo de cinco níveis proporciona a flexibilidade e granularidade necessárias para fornecer o sistema de proteção que reflete a estrutura da sua organização.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagrama mostrando sensores e relação regional.":::

Utilizando a Enterprise View, pode editar os seus sites diretamente. Quando seleciona um site a partir do Enterprise View, o número de alertas abertos aparece ao lado de cada zona.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Screenshot de um mapa de consola de gestão no local com sobreposição de dados de Berlim.":::

Para configurar um site:

1. Adicione novas unidades de negócio para refletir a estrutura lógica da sua organização.

   1. A partir da vista Enterprise, selecione **All Sites** Manage  >  **Business Units**.

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="Selecione gerir a unidade de negócio a partir do menu drop down de todos os sites no ecrã de visualização da empresa.":::

   1. Introduza o nome da nova unidade de negócio e selecione **ADD**.

1. Adicione novas regiões para refletir as regiões da sua organização.

   1. A partir da Visão Empresarial, selecione **Todas as**  >  **Regiões De Gestão de Regiões.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="Selecione todas as regiões e, em seguida, gere regiões para gerir as regiões do seu empreendimento.":::

   1. Introduza o nome da nova região e selecione **ADD**.

1. Adicione um site.

   1. A partir da vista Enterprise, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: na barra superior. O seu cursor aparece como um sinal de mais **+** ().

   1. Posicione **+** a localização do novo site e selecione-o. Abre-se a caixa de diálogo **Create New Site.**

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Screenshot da vista Criar Novo Site.":::

   1. Defina o nome e o endereço físico para o novo site e selecione **SAVE**. O novo site aparece no mapa do site.

4. [Adicione zonas a um site.](#create-enterprise-zones)

5. [Ligue os sensores.](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console)

6. [Atribua o sensor às zonas do local.](#assign-sensors-to-zones)

### <a name="delete-a-site"></a>Eliminar um Site

Se já não precisar de um site, pode eliminá-lo da sua consola de gestão no local.

Para eliminar um site:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome do site e, em seguida, selecione **Delete Site**. A caixa de confirmação aparece, verificando se pretende eliminar o site.

2. Na caixa de confirmação, selecione **CONFIRM**.

## <a name="create-enterprise-zones"></a>Criar zonas empresariais

As zonas são entidades lógicas que permitem dividir os dispositivos dentro de um site em grupos de acordo com várias características. Por exemplo, pode criar grupos para linhas de produção, subestações, áreas de site ou tipos de dispositivos. Você pode definir zonas com base em qualquer característica que seja adequada para a sua organização.

Configura zonas como parte do processo de configuração do site.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Screenshot da vista das Zonas de Gestão do Site.":::

A tabela seguinte descreve os parâmetros na janela **de Gestão** do Local.

| Parâmetro | Descrição |
|--|--|
| Nome | O nome do sensor. Só pode alterar este nome a partir do sensor. Para mais informações, consulte o Guia do Utilizador Defender para IoT. |
| IP | O endereço IP do sensor. |
| Versão | A versão do sensor. |
| Conectividade | O estado de conectividade do sensor. O estado pode ser **ligado** ou **desligado**. |
| Última Atualização | A data da última atualização. |
| Atualizar o Progresso | A barra de progresso mostra o estado do processo de atualização, da seguinte forma:<br />- Pacote de upload<br />- Preparação para instalar<br />- Parar processos<br />- Backup de dados<br />- Tirar fotografias<br />- Configuração de atualização<br />- Atualização das dependências<br />- Atualizar bibliotecas<br />- Bases de dados de remendação<br />- Iniciar processos<br />- Validação da sanidade do sistema<br />- Validação conseguiu<br />- Sucesso<br />- Falha<br />- A atualização começou<br />- Início da instalaçãoogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Para obter mais informações sobre a atualização, consulte o [Microsoft Support](https://support.microsoft.com/) para obter ajuda. |
| Dispositivos | O número de dispositivos OT que o sensor monitoriza. |
| Alertas | O número de alertas no sensor. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Permite atribuir um sensor a zonas. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Permite eliminar um sensor desligado do local. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Indica quantos sensores estão atualmente ligados à zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Indica quantos ativos OT estão atualmente ligados à zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Indica o número de alertas enviados por sensores que são atribuídos à zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Unassignimentos sensores de zonas. |

Para adicionar uma zona a um site:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome do site e, em seguida, selecione **Add Zone**. Aparece a caixa de diálogo **Create New Zone.**

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Screenshot da vista Criar Nova Zona.":::

1. Insira o nome da zona.

1. Introduza uma descrição para a nova zona que indique claramente as características que usou para dividir o site em zonas.

1. Selecione **SAVE**. A nova zona aparece na janela **de Gestão** de Sítios sob o site a que esta zona pertence.

Para editar uma zona:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome da zona e, em seguida, selecione **Edit Zone**. Aparece a caixa de diálogo zona de **edição.**

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Screenshot que mostra a caixa de diálogo da Zona de Edição.":::

1. Edite os parâmetros da zona e selecione **SAVE**.

Para eliminar uma zona:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome da zona e, em seguida, selecione **Zona de Eliminação**.

1. Na caixa de confirmação, selecione **SIM**.

Para filtrar de acordo com o estado de conectividade:

- A partir do canto superior esquerdo, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: ao lado da **Conectividade** e, em seguida, selecione uma das seguintes opções:

  - **Todos**: Apresenta todos os sensores que reportam a esta consola de gestão no local.

  - **Ligado**: Apresenta apenas sensores ligados.

  - **Desligado**: Apresenta apenas sensores desligados.

Para filtrar de acordo com o estado de atualização:

- A partir do canto superior esquerdo, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: ao lado do Estado de **atualização** e selecione uma das seguintes opções:

  - **Todos**: Apresenta todos os sensores que reportam a esta consola de gestão no local.

  - **Válido:** Apresenta sensores com um estado de atualização válido.

  - **Em Curso**: Apresenta sensores que estão em fase de atualização.

  - **Falha:** Apresenta sensores cujo processo de atualização falhou.

## <a name="assign-sensors-to-zones"></a>Atribuir sensores a zonas

Para cada zona, é necessário atribuir sensores que realizem análises e alertas de tráfego local. Só é possível atribuir os sensores que estão ligados à consola de gestão no local.

Para atribuir um sensor:

1. Selecione **Gestão de Sites**. Os sensores não atribuídos aparecem no canto superior esquerdo da caixa de diálogo.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Screenshot da vista dos sensores não atribuídos.":::

1. Verifique se o estado **de conectividade** está ligado. Caso contrário, consulte [os sensores de ligação à consola de gestão no local](#connect-sensors-to-the-on-premises-management-console) para obter mais detalhes sobre a ligação. 

1. Selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: para o sensor que pretende atribuir.

1. Na caixa de diálogo **do sensor de atribuição,** selecione a unidade de negócio, região, site e zona a atribuir.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Screenshot da vista do sensor de atribuição.":::

1. Selecione **ATRIBUÇÃO**.

Para não assinar e eliminar um sensor:

1. Desligue o sensor da consola de gestão no local. Consulte os sensores de [ligação à consola de gestão no local](#connect-sensors-to-the-on-premises-management-console) para obter mais detalhes.

1. Na janela **'Gestão** do Local', selecione o sensor e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . O sensor aparece na lista de sensores não atribuídos após alguns momentos.

1. Para eliminar o sensor não atribuído do site, selecione o sensor da lista de sensores não atribuídos e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="see-also"></a>Ver também

[Resolver problemas do sensor e da consola de gestão no local](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)

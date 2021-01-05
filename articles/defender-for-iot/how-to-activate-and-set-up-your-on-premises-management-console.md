---
title: Ative e crie a sua consola de gestão no local
description: A ativação e configuração de consolas de gestão garante que os sensores estão registados no Azure e enviam informações para a consola de gestão no local, e que a consola de gestão no local realiza tarefas de gestão em sensores conectados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/24/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 56092d61e1eed4e4ebe4f8cb7f97d01e91e8543e
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843816"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Ative e crie a sua consola de gestão no local 

Ativação e configuração da consola de gestão no local garantem que:

- Os dispositivos de rede que está a monitorizar através de sensores conectados estão registados numa conta Azure.

- Os sensores enviam informações para a consola de gestão no local.

- A consola de gestão no local realiza tarefas de gestão em sensores conectados.

- Instalou um certificado SSL.

## <a name="sign-in-for-the-first-time"></a>Sign in for the first time (Iniciar sessão pela primeira vez)

Para iniciar sinsução na consola de gestão:

- Abra um navegador web e introduza o endereço IP e a palavra-passe que recebeu para a consola de gestão no local durante a instalação do sistema. Se esqueceu a sua palavra-passe, selecione **Recuperar a Palavra-passe** e ver [a recuperação da palavra-passe](how-to-manage-the-on-premises-management-console.md#password-recovery).

## <a name="upload-an-activation-file"></a>Faça upload de um ficheiro de ativação

Após o início de sing-in, ative a consola de gestão no local, descarregando um ficheiro de ativação a partir da página de **Preços** do portal Azure Defender para IoT. Este ficheiro contém os dispositivos comprometidos agregados definidos durante o processo de embarque. **Dispositivos comprometidos** indicam o número de dispositivos que o Defender para IoT irá monitorizar por subscrição.

Para carregar um ficheiro de ativação:

1. Vá ao Defender para **a** página de preços IoT.
1. Selecione **o ficheiro de ativação Descarregue para o** separador consola de gestão. O ficheiro de ativação é descarregado.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Descarregue o ficheiro de ativação.":::

1. Selecione **Definições** do Sistema a partir da consola de gestão.
1. **Selecione ativação**.
1. **Selecione Escolha um Ficheiro** e selecione o ficheiro que guardou.

Após a ativação inicial, o número de dispositivos monitorizados pode exceder o número de dispositivos comprometidos definidos durante o embarque. Isto pode acontecer, por exemplo, se ligar mais sensores à consola de gestão. Se houver uma discrepância entre o número de dispositivos monitorizados e o número de dispositivos comprometidos, aparece um aviso na consola de gestão. Se isto acontecer, deve carregar um novo ficheiro de ativação.

## <a name="set-up-a-certificate"></a>Configurar um certificado

Após a instalação da consola de gestão, é gerado um certificado auto-assinado local e utilizado para aceder à consola. Depois de um administrador entrar pela primeira vez na consola de gestão, esse utilizador é solicitado a embarcar num certificado SSL/TLS. Recomendamos que trabalhe com um certificado de ca-assinado fidedigno e não utilize o certificado auto-assinado gerado localmente.

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

Poderá ter de refrescar o ecrã depois de ter enviado o certificado assinado pela AC.

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

### <a name="connect-sensors-from-the-sensor-console"></a>Ligue os sensores da consola do sensor

Para ligar sensores específicos à consola de gestão no local a partir da consola sensorial:

1. No painel esquerdo da consola do sensor, selecione **Definições do Sistema**.

2. Selecione **Ligação à Gestão**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Screenshot da janela de estado de uma consola de gestão no local, mostrando Unconnected.":::

3. Na caixa de texto **do Endereço,** insira o endereço IP da consola de gestão no local à qual pretende ligar.

4. Selecione **Ligar**. O estado muda:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Screenshot da janela de estado de uma consola de gestão no local, mostrando Connected.":::

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

O mapa padrão da empresa fornece uma visão geral dos seus ativos de acordo com vários níveis de localizações geográficas.

A visão dos seus ativos poderá ser necessária quando a estrutura organizacional e as permissões do utilizador são complexas. Nestes casos, a configuração do site pode ser determinada por uma estrutura organizacional global, além da estrutura padrão do site ou da zona.

Para apoiar este ambiente, você precisa criar uma topologia de negócios global baseada nas unidades de negócio, regiões, locais e zonas da sua organização. Também é necessário definir permissões de acesso ao utilizador em torno destas entidades, utilizando grupos de acesso.

Os grupos de acesso permitem um melhor controlo sobre onde os utilizadores gerem e analisam os ativos na plataforma Defender para IoT.

### <a name="how-it-works"></a>Como funciona

Para cada site, pode definir uma unidade de negócio e uma região. Em seguida, pode adicionar zonas, que são entidades lógicas na sua rede. 

Para cada zona, deve atribuir pelo menos um sensor. O modelo de cinco níveis proporciona a flexibilidade e granularidade necessárias para fornecer o sistema de proteção que reflete a estrutura da sua organização.

Pode editar os seus sites diretamente a partir de qualquer uma das vistas do mapa. Quando abre um site a partir de uma vista de mapa, o número de alertas abertos aparece ao lado de cada zona.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Screenshot de um mapa de consola de gestão no local com sobreposição de dados de Berlim.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagrama mostrando sensores e relação regional.":::

Para configurar um site:

1. Adicione novas unidades de negócio para refletir a estrutura lógica da sua organização.

2. Adicione novas regiões para refletir as regiões da sua organização.

3. Adicione um site.

4. Adicione zonas a um site.

5. Ligue os sensores.

6. Atribua o sensor às zonas do local.

Para adicionar unidades de negócio:

1. A partir da vista Enterprise, selecione **All Sites** Manage  >  **Business Units**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Screenshot mostrando a vista 'Gerir Unidades de Negócio'.":::

2. Introduza o nome da nova unidade de negócio e selecione **ADD**.

Para adicionar uma nova região:

1. A partir do ponto de vista da Empresa, selecione **Todas as**  >  **Regiões De Gestão de Regiões.**

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Screenshot mostrando a vista De Regiões de Gestão.":::

2. Introduza o nome da nova região e selecione **ADD**.

Para adicionar um novo site:

1. A partir da vista Enterprise, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: na barra superior. O seu cursor aparece como um sinal de mais **+** ().

2. Posicione **+** a localização do novo site e selecione-o. Abre-se a caixa de diálogo **Create New Site.**

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Screenshot da vista Criar Novo Site.":::

3. Defina o nome e o endereço físico para o novo site e selecione **SAVE**. O novo site aparece no mapa do site.

Para eliminar um site:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome do site e, em seguida, selecione **Delete Site**. A caixa de confirmação aparece, verificando se pretende eliminar o site.

2. Na caixa de confirmação, selecione **SIM**. A caixa de confirmação fecha e a janela **de Gestão** do Site aparece sem o site que apagou.

## <a name="create-enterprise-zones"></a>Criar zonas empresariais

As zonas são entidades lógicas que lhe permitem dividir os ativos dentro de um site em grupos de acordo com várias características. Por exemplo, pode criar grupos para linhas de produção, subestações, áreas de site ou tipos de ativos. Você pode definir zonas com base em qualquer característica que seja adequada para a sua organização.

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
| Elementos | O número de ativos OT que o sensor monitoriza. |
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

2. Insira o nome da zona.

3. Introduza uma descrição para a nova zona que indique claramente as características que usou para dividir o site em zonas.

4. Selecione **SAVE**. A nova zona aparece na janela **de Gestão** de Sítios sob o site a que esta zona pertence.

Para editar uma zona:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome da zona e, em seguida, selecione **Edit Zone**. Aparece a caixa de diálogo zona de **edição.**

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Screenshot que mostra a caixa de diálogo da Zona de Edição.":::

2. Edite os parâmetros da zona e selecione **SAVE**.

Para eliminar uma zona:

1. Na janela **'Gestão** do Site', selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: a partir da barra que contém o nome da zona e, em seguida, selecione **Zona de Eliminação**.

2. Na caixa de confirmação, selecione **SIM**.

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

2. Verifique se o estado **de conectividade** está ligado. Caso contrário, consulte [os sensores de ligação à consola de gestão no local](#connect-sensors-to-the-on-premises-management-console) para obter mais detalhes sobre a ligação. 

3. Selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: para o sensor que pretende atribuir.

4. Na caixa de diálogo **do sensor de atribuição,** selecione a unidade de negócio, região, site e zona a atribuir.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Screenshot da vista do sensor de atribuição.":::

5. Selecione **ATRIBUÇÃO**.

Para não assinar e eliminar um sensor:

1. Desligue o sensor da consola de gestão no local. Consulte os sensores de [ligação à consola de gestão no local](#connect-sensors-to-the-on-premises-management-console) para obter mais detalhes.

2. Na janela **'Gestão** do Local', selecione o sensor e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . O sensor aparece na lista de sensores não atribuídos após alguns momentos.

3. Para eliminar o sensor não atribuído do site, selecione o sensor da lista de sensores não atribuídos e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="see-also"></a>Veja também

[Resolução de problemas da consola de gestão de sensores e no local](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)

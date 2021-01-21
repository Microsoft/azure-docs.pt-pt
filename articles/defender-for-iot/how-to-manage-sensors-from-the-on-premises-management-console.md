---
title: Gerir sensores na consola de gestão no local
description: Saiba como gerir os sensores a partir da consola de gestão, incluindo atualizar versões de sensores, empurrar as definições do sistema para os sensores e ativar e desativar os motores nos sensores.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 73eb693ab9f06535fd523eb386969e2cce961eef
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624596"
---
# <a name="manage-sensors-from-the-management-console"></a>Gerir sensores a partir da consola de gestão

Este artigo descreve como gerir os sensores a partir da consola de gestão, incluindo:

- Empurre as definições do sistema para os sensores

- Ativar e desativar os motores nos sensores

- Atualizar versões de sensores

## <a name="push-configurations"></a>Configurações push

Pode definir várias definições do sistema e aplicá-las automaticamente aos sensores que estão ligados à consola de gestão. Isto poupa tempo e ajuda a garantir configurações simplificadas em todos os sensores da empresa.

Pode definir as seguintes definições do sistema de sensores a partir da consola de gestão:

- Servidor de correio

- Monitorização do MIB do SNMP

- Active Directory

- Definições de DNS

- Sub-redes

- Pseudónimos portuários

Para aplicar as definições do sistema:

1. No painel esquerdo da consola, selecione **Definições do Sistema**.

2. No painel de **sensores de configuração,** selecione uma das opções.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="As opções de definição do sistema para um sensor.":::

   O exemplo a seguir descreve como definir parâmetros de servidor de correio para os sensores da empresa.

3. Selecione **servidor de correio**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Selecione o seu servidor de correio a partir do ecrã 'Definições do Sistema'.":::

4. Selecione um sensor à esquerda.

5. Desfie os parâmetros do servidor de correio e **selecione Duplicate**. Cada item na árvore sensorial aparece com uma caixa de verificação ao lado.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Certifique-se de que as caixas de verificação são selecionadas para os seus sensores.":::

6. Na árvore do sensor, selecione os itens aos quais pretende aplicar a configuração.

7. Selecione **Guardar**.

## <a name="update-versions"></a>Versões de atualização

Pode atualizar vários sensores simultaneamente a partir da consola de gestão no local.

Para atualizar vários sensores:

1. Aceda ao [portal do Azure](https://portal.azure.com/).

2. Vá ao Azure Defender para ioT.

3. Aceda à página **'Atualizações'.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Screenshot da vista do painel de instrumentos updates.":::

4. Selecione **Descarregar** na secção **Sensores** e guardar o ficheiro.

5. Inscreva-se na consola de gestão e selecione **Definições do Sistema**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Screenshot do menu Administração para selecionar Definições do Sistema.":::

6. Marque os sensores que pretende atualizar na secção **de Configuração** do Motor do Sensor e, em seguida, selecione **Atualizações Automáticas**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Dois sensores que mostram o modo de aprendizagem e as atualizações automáticas.":::

7. Selecione **Guardar alterações**.

8. No painel de atualização da **versão sensorial,** selecione :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Ecrã de atualização da versão do sensor para exibir ficheiros.":::

9. Abre-se uma caixa de diálogo **de ficheiros** de upload. Faça o upload do ficheiro que descarregou a partir da página **'Actualizações'.**

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Selecione o botão Procurar para carregar o seu ficheiro.":::

10. Durante o processo de atualização, o estado de atualização de cada sensor aparece na janela **De Gestão** do Site.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observe o progresso da sua atualização.":::

## <a name="update-threat-intelligence-packages"></a>Atualizar pacotes de inteligência de ameaças 

O pacote de dados para a inteligência de ameaças é fornecido com cada novo Defender para a versão IoT, ou se necessário entre lançamentos. O pacote contém assinaturas (incluindo assinaturas de malware), CVEs e outros conteúdos de segurança. 

Pode carregar manualmente este ficheiro a partir da página **'Atualizações'** do portal IoT e atualizá-lo automaticamente para os sensores. 

Para atualizar os dados da inteligência de ameaça: 

1. Aceda à página Defender para **atualizações** ioT. 

2. Faça o download e guarde o ficheiro.

3. Inscreva-se na consola de gestão. 

4. No menu lateral, selecione **Definições do Sistema**. 

5. Selecione os sensores que devem receber a atualização na secção **de Configuração do Motor do Sensor.**  

6. Na secção **Select Threat Intelligence Data,** selecione o sinal mais **+** (). 

7. Faça o upload do pacote que descarregou na página Defender para **IoT Updates.**

## <a name="understand-sensor-disconnection-events"></a>Compreender eventos de desconexão de sensores

A janela **do Gestor do Site** exibe informações de desconexão se os sensores desligarem da consola de gestão no local. Estão disponíveis as seguintes informações de desconexão do sensor:

- "A consola de gestão no local não pode processar os dados recebidos do sensor."

- "Os tempos de deriva detetados. A consola de gestão no local foi desligada do sensor."

- "Sensor não comunicando com a consola de gestão no local. Verifique a conectividade da rede ou a validação do certificado."

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Screenshot da vista da zona 1.":::

Pode enviar alertas a terceiros com informações sobre sensores desligados. Para obter mais informações, consulte [alertas de falha do sensor para a frente](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Ativar ou desativar sensores

Os sensores estão protegidos por cinco motores Defender para motores IoT. Pode ativar ou desativar os motores para sensores ligados.

| Motor | Descrição | Cenário de exemplo |
|--|--|--|
| Motor de violação de protocolo | Uma violação do protocolo ocorre quando a estrutura do pacote ou os valores de campo não cumprem a especificação do protocolo. | alerta "Operação Ilegal de MODBUS (Código de Função Zero)". Este alerta indica que um dispositivo primário enviou um pedido com o código de função 0 para um dispositivo secundário. Isto não é permitido de acordo com a especificação do protocolo, e o dispositivo secundário pode não manusear corretamente a entrada. |
| Motor de violação de políticas | Uma violação de política ocorre com um desvio do comportamento básico definido na política aprendida ou configurada. | Alerta "Agente de Utilizador HTTP NÃO Autorizado". Este alerta indica que uma aplicação que não tenha sido aprendida ou aprovada pela apólice é utilizada como cliente HTTP num dispositivo. Este pode ser um novo navegador web ou aplicação nesse dispositivo. |
| Motor de malware | O motor de malware deteta atividade de rede maliciosa. | Alerta "Suspeita de Atividade Maliciosa (Stuxnet)". Este alerta indica que o sensor encontrou atividades suspeitas de rede conhecidas por estarem relacionadas com o malware Stuxnet, que é uma ameaça persistente avançada direcionada ao controlo industrial e redes SCADA. |
| Motor de anomalia | O motor de malware deteta uma anomalia no comportamento da rede. | "Comportamento periódico no Canal de Comunicação.". Este é um componente que inspeciona as ligações de rede e encontra comportamento periódico ou cíclico da transmissão de dados, que é comum nas redes industriais. |
| Motor operacional | Este motor deteta incidentes operacionais ou entidades com mau funcionamento. | `Device is Suspected to be Disconnected (Unresponsive)` alerta. Este alerta foi desencadeado quando um dispositivo não está a responder a quaisquer pedidos para um período predefinido. Pode indicar uma paragem do dispositivo, desconexão ou mau funcionamento.
|

Para ativar ou desativar os motores para sensores ligados:

1. No painel esquerdo da consola, selecione **Definições do Sistema**.

2. Na secção **de Configuração do Motor do Sensor,** selecione **Ativar** ou **desativar** os motores.
         
3. SELECIONE **GUARDAR ALTERAÇÕES**.

   Um ponto de exclamação vermelho aparece se houver uma incompatibilidade de motores ativados num dos sensores da empresa. O motor pode ter sido desativado diretamente do sensor.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Desfasamento dos motores ativados."::: 

## <a name="define-sensor-backup-schedules"></a>Defina horários de backup de sensores

Pode agendar cópias de segurança do sensor e realizar cópias de segurança a pedido a partir da consola de gestão no local. Isto ajuda a proteger contra falhas de disco rígido e perda de dados.

- O que está apoiado: Configurações e dados.

- O que não está apoiado: ficheiros e registos PCAP. Pode fazer o back-up manualmente e restaurar PCAPs e registos.

Por padrão, os sensores são automaticamente apoiados às 3:00 da manhã diariamente. A funcionalidade de agendamento de backup para sensores permite-lhe recolher estas cópias de segurança e armazená-las na consola de gestão no local ou num servidor externo de backup. Copiar ficheiros de sensores para a consola de gestão no local acontece num canal encriptado.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Uma visão do ecrã de reserva do sensor.":::

Quando a localização de backup do sensor predefinido é alterada, a consola de gestão no local recupera automaticamente os ficheiros a partir da nova localização do sensor ou de uma localização externa, desde que a consola tenha permissão para aceder à localização. 

Quando os sensores não estão registados na consola de gestão no local, a caixa de diálogo **de agenda de cópias de segurança** do sensor indica que não são geridos sensores.  

O processo de restauro é o mesmo independentemente do local onde os ficheiros estão armazenados.

### <a name="backup-storage-for-sensors"></a>Armazenamento de backup para sensores

Pode utilizar a consola de gestão no local para manter até nove backups para cada sensor gerido, desde que os ficheiros de backup não excedam o espaço máximo de backup que está atribuído. 

O espaço disponível é calculado com base no modelo de consola de gestão com o qual está a trabalhar: 

- **Modelo de produção**: O armazenamento predefinido é de 40 GB; limite é de 100 GB. 

- **Modelo médio**: O armazenamento predefinido é de 20 GB; limite é de 50 GB. 

- **Modelo portátil**: O armazenamento predefinido é de 10 GB; limite é de 25 GB. 

- **Modelo fino**: O armazenamento predefinido é de 2 GB; limite é de 4 GB. 

- **Modelo robusto**: O armazenamento predefinido é de 10 GB; limite é de 25 GB. 

A atribuição por defeito é apresentada na caixa de diálogo **de agenda de cópias de segurança** do sensor. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="O ecrã de configuração do servidor de correio eletrónico de edição.":::

Não há limite de armazenamento quando se está a fazer backup para um servidor externo. No entanto, deve definir um limite de alocação superior no campo **'Caminho Personalizado'** de agenda de cópias de segurança do  >   sensor. Os seguintes números e caracteres são suportados: `/, a-z, A-Z, 0-9, and _` . 

Aqui estão informações sobre a exceção dos limites de armazenamento de atribuição:

- Se exceder o espaço de armazenamento atribuído, o sensor não está apoiado. 

- Se estiver a fazer backup de mais de um sensor, a consola de gestão tenta recuperar ficheiros de sensores para os sensores geridos.  

- Se a recuperação de um sensor exceder o limite, a consola de gestão tenta obter informações de backup a partir do sensor seguinte. 

Quando excede o número de cópias de segurança definidas, o ficheiro de backup mais antigo é eliminado para acomodar o novo.

Os ficheiros de cópia de segurança do sensor são automaticamente nomeados no seguinte formato: `<sensor name>-backup-version-<version>-<date>.tar` . Por exemplo: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Para fazer o reforço dos sensores:

1. Selecione **a cópia** de segurança do sensor de agenda da janela **Definições do sistema.** Os sensores que a consola de gestão no local gere aparecem na caixa de diálogo **de agenda de cópias de segurança** do sensor.  

2. Ativar o **alternador de cópias de segurança** de recolha.  

3. Selecione um intervalo de calendário, data e fuso horário. O formato de tempo baseia-se num relógio de 24 horas. Por exemplo, insira 18:00 às **18:00**. 

4. No campo **de atribuição de armazenamento de cópias de segurança,** insira o armazenamento que pretende atribuir para as suas cópias de segurança. É notificado se exceder o espaço máximo.

5. No campo **Retenção Última,** indique o número de cópias de segurança por sensor que pretende reter. Quando o limite é ultrapassado, a cópia de segurança mais antiga é eliminada.  

6. Escolha uma localização de reserva:  

   - Para voltar à consola de gestão no local, desative o **alternador Do Caminho Personalizado.** A localização predefinida é `/var/cyberx/sensor-backups` .  

   - Para fazer uma parte de trás para um servidor externo, ative o **caminho personalizado** para alternar e entrar num local. Os seguintes números e caracteres são suportados: `/, a-z, A-Z, 0-9, and, _` . 

7. Selecione **Guardar**. 

Para recuar imediatamente: 

- Selecione **Back Up Agora**. A consola de gestão no local cria e recolhe ficheiros de backup de sensores. 

### <a name="receiving-backup-notifications-for-sensors"></a>Receber notificações de backup para sensores 

A caixa de diálogo **de agendamento de cópia de segurança** do sensor e o registo de cópia de segurança listam automaticamente informações sobre sucessos e falhas de backup.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Veja os seus sensores e onde estão localizados e todas as informações relevantes.":::

Falhas podem ocorrer porque:    

- Nenhum ficheiro de reserva foi encontrado. 

- Um ficheiro foi encontrado, mas não pode ser recuperado.  

- Há uma falha de ligação à rede. 

- Não há espaço suficiente atribuído à consola de gestão no local para completar a cópia de segurança.  

Pode enviar uma notificação por e-mail, atualizações de syslog e notificações do sistema quando ocorre uma falha. Para tal, crie uma regra de encaminhamento em **Notificações do Sistema**. 

### <a name="restoring-sensors"></a>Restaurar sensores 

Pode restaurar cópias de segurança a partir da consola de gestão no local e utilizando o CLI.  

Para restaurar a partir da consola: 

- Selecione **Restaurar a imagem** a partir da janela de definição do sistema de **sensores.**

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Restaurar uma cópia de segurança da sua imagem.":::

  Em seguida, a consola exibe falhas de restauro.  

Para restaurar utilizando o CLI: 

- Inscreva-se numa conta administrativa e `$ sudo cyberx-xsense-system-restore` entre. 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Guarde uma cópia de segurança de um sensor para um servidor SMB externo

Para configurar um servidor SMB para que possa guardar uma cópia de segurança do sensor para uma unidade externa: 

1. Crie uma pasta partilhada no servidor SMB externo. 

2. Obtenha o caminho da pasta, nome de utilizador e senha necessária para aceder ao servidor SMB. 

3. No Defender for IoT, faça um diretório para os backups: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Editar fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Editar ou criar credenciais para partilhar. Estas são as credenciais para o servidor SMB: 

   `sudo nano /etc/samba/user` 

6. Adicionar:  

   `username=<user name>` 

   `password=<password>` 

7. Monte o diretório: 

   `sudo mount -a` 

8. Configure um diretório de reserva para a pasta partilhada no sensor Defender para IoT:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Definir `Backup.shared_location` para `<backup_folder_name_on_cyberx_server>` .

## <a name="see-also"></a>Ver também

[Gerir sensores individuais](how-to-manage-individual-sensors.md)

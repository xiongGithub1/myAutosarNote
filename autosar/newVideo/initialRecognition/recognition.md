<!--
 * @Author: qinXiong
 * @Date: 2025-01-08 20:50:10
 * @LastEditors: xiongGithub1&&qx20001119@163.com
 * @LastEditTime: 2025-01-08 21:54:42
 * @Description: 
-->

# ѧϰ����
## 1.7.Autosarѧϰָ���㲦
1.7.1������Լ���λ
1.7.2ǰ��֪ʶ�ͼ���Ҫ��
1.7.3Ҫ�мܹ���ʶ
1.7.4��Ҫ�Ǻ�רҵ����ʻ�
1.7.5��Ҫץ���ʶ��Ṥ��
(1)AutosarCP��AP
(2)Autosar���汾ϸ�ڲ��쵽˼��ͳһ
(3)Autosar������ҵ�������
(4)�Զ����ɴ�����ֹ��༭������и�ͽ�� 

## 2.1.Autosar��ϸ��ѧϰ��Դ
2.1.1������
(1)https://www.autosar.org/
(2)�������:�ص���standards�µļ���Autosar��׼
2.1.2����׼�ĵ�
(1)��׼�ĵ����
(2)���γ�ѡ��19-11��Ϊ�ο�
(3)��ʾʹ��:general/AUTOSAR_EXP_LayeredsoftwareArchitecture.pdf
2.1.3����Դ����
(1)arctic_core_autosar cp_project����
(2)Դ�빤�̽��������
2.1.4����������
(1)2.6.1���Ƽ���2������blog
(2)���ձ�ؼ���ȥ�����������ϡ�

## 2.2.AutoSarCP�ֲ�ͷ�ģ�����
2.2.1��ר�дʻ�
(1)Autosar��׼������ֺ͸���
(2)ר����д�����ض����壬���������Ĺؼ�\
(3)���齨������ר�дʻ��
2.2.2������׼pdf��ʾ
(1)���н��
(2)��Ϸ������
2.2.3��AutosarCP��3�����ȷֲ�
(1)��3��:App��RTE��BSW
(2)BSW��3��4����:service�㣬ECU abstraction�㣬MCAL����������
(3)BSW�����ģ��

ϵͳ����㣬�洢�����ܣ����ͨ�ţ�ͨѶ��IO����㣬����������

## 2.4.AutosarCP������ֲ���
2.4.1��MCAL
2.4.2��ECU�����
2.4.3����������
2.4.4��Services��
2.4.5��RTE
2.4.6��AppL

## 2.7.AutoSarCP�����ļ�������
2.7.1��types of services�� System Services ��Memory Services��Crypto Services Off-Board Communication Services ��Communication Services��I/O Hardware Abstraction��Complex Drivers

2.7.2��internal driver��MCAL
2.7.3��external driver��ECU�����
2.7.4��interface��ECU�����
2.7.5��handler
2.7.6��manager
2.7.7��libraries

## 3.1.AutosarCP����ϵͳѧϰ����
3.1.1��������
(1)AutosarCP��0S��RT0S��һ�֣����Ǻ͵��͹�ҵ���Ѽ�����freertos���в��죬Ҳ���˺ܶ�ѧϰ��
(2)AutoSarcP��0S����0SEKOS��������һЩ��չ��������ѧ0SEKOS���������ѧ��չ��
(3)0SEK��Autosarֻ�涨��0S�����Ժ���Ϊ��
ʵ���ǽ�������̵��¶���Ҫô����Ҫô����
3.1.2��OS���ļ�ֵ
(1)Autosarϵͳ��"�ɻ�"����Application��BSW����OS��֧����ϵ�����ɡ������ԵĻ����.
(2)OS��Ҫ��Application��BSW�򽻵�������2�ߵ�����entities���䵽OS��task������ʵ�֣�����OS��CPU�Ĺ���
(3)OS����Ӳ���ж��й���ʵ��ISR���ƣ���Ҳ��Ϊ�˷���Application��BSW��os���ʱ�˴˶��Ƕ����ģ����Ǳ�׼API��ͨ���������ɡ�RTE���Խ�
(4)Autosar��ϵ�£�application��bsW��os���ʱ�˴˶��Ƕ����ģ����Ǳ�׼API��ͨ���������ɡ�RTE���Խӡ�
3.1.3�����γ̵�Ŀ��ͼ�ֵ
(1)�������ѧ�ͷ����������AutosarCP��0s��Application��BSW�����Э���������������������Autosar
(2)����ĵ��ͽ��⣬������0S�ĺ��ĸ������Ȼ��ơ�����������ȼ���alarm��resources�ȡ�
(3)����ĵ��ʹ��룬��AutoSarcP��ϵ��ʵ����ֱ����ʶ��ӦƸ�͹����л�þ������ơ�

3.1.4�����γ���ν����ѧ
(1)����1:��AUTOSAR Classic����ϵͳ-���ѵ�.pptx��
(2)����2:��IS017356-3�����.docx��
(3)����3:Autosar�ٷ��ĵ���0S����
(4)����4:��Դ����arctic core
(5)ѧϰҪ��:��һ��0S������ѧ��autosarǰ��2���γ̣���һ����Ƭ��Ƕ��ʽ��C���Ի���
<!--
 * @Author: qinXiong
 * @Date: 2024-11-26 16:13:59
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-26 16:27:56
 * @Description: 
-->


# AutoSAR Ӧ��������  

## 1.ʲô�� RTE �㡣

AutoSAR Cp�������ܹ��ֳ�����:ARRL��RTE��BSW

![20241126161706](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126161706.png)

AppL����Ӧ�ò����˼��RTE��������ʱ������BSW���ǻ���������

����RTE��Ĵ��ڿ��Խ���AppL��BSW�㣬�����߿���ͬ����������󼯳ɵ�һ��RTEΪӦ���ṩ����ʱ������

RTE�������е���绰����Ա�����Խ�һ��SWC����Ϣͨ��RTE�ӵ�����SWC����BSW�ϡ�RTE���й�����Щ��Ϣ�Ĺ��ܡ�
## 2.RTE ������á�

1. ͨ�Ź���(ECU�ڲ�(ͨ��ȫ�ֱ���)�Ϳ�ECU(ͨ������))

    S/Rͨ��(Sender-Receiver)����SWC֮�䡢SWC��BSW֮�佻������
    C/Sͨ��(Client-Server)����SWC֮�䡢SWC��BSW֮��ĺ������ã��ͻ��˵��÷������ṩ�ĺ���

2. ģʽ����:����SWC֮�䡢SWC��BSWģ��֮���������ģʽ�Ľ��� ����ECU��Դ״̬������ͨ�����ߵ�ģʽ
3. ����һ���Ա�����

- IRV(Inter RunnableVariables)������SWC�ڲ���Runnable֮������ݽ�����
- Exclusive Areas(EAs)���ٽ�����������װ�ں��ṩ���ٽ�����������(ԭ�Ӳ��������жϣ�Spinlock��Resource)ΪSWC��RTE�ڲ������ṩ����һ���Ա���

4. ����RTE��Runnables����֧��

![20241126162405](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126162405.png)

- ͨ�� RTE ��runnable �ṩ�����¼���ͨ�� RTE ��ʵ����������͵���runnable
- ͨ�� RTE �� runnable �ṩ������Դ����runnable ��Ҫ��һЩ��Դͨ���ӿڴ������
- ��BSW��SWC�����������OS��runnablesҲ�������ˣ�runnable������������ RTE �ṩ��������OSֱ���ṩ



## 3. �ܽᡣ

RTE ��Ϊautosar ���ɻ�ȱ��һ�㣬��һ��������ڹ����������У��� BSW ��Ӧ�ò�ӳ��ã��ͻ��Զ�����RTE���롣��Ҫ�ṩ������4����ơ�
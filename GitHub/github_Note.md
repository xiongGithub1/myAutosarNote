<!--
 * @Author: qinXiong
 * @Date: 2024-11-27 08:50:04
 * @LastEditors: Qxiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-27 08:51:49
 * @Description: 
-->


# github ʹ�ñʼ��Լ�������


## ��������ssh��Կ

- �����Զ�ֿ̲���ȡʱ������û�з���Ȩ�޵���

```git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```
*** 

https://blog.csdn.net/W_317/article/details/106518894

## �����Ĵ�����ȡ�ϲ�����
```// �鿴Զ��
git remote -v 
// ��ȡԶ��ָ����֧��������ʱ�½��ķ�֧
// ��ȡԶ��master�ķ�֧�Ĵ��뵽��ʱ�½���temp
git fetch origin master:temp
// �鿴�汾����
// �鿴temp��֧�뵱ǰ��֧�Ĳ���
git diff temp 
// ����ʱ��֧temp�ϲ�����ǰ��֧
git merge tmep
// ɾ����ʱ��֧
git branch -D temp
```

## �ϲ���֧
```
// �л�������֧
git checkout main

// �ϲ���֧
git merge origin/��֧

//�����ͻ����

//�����ϴ�����֧
git push origin main
```
## �����¡�����ϴ���Ŀ���ֿ�İ취��

git remote set-url origin git@github.com:xiongGithub1/myAutosarNote.git

git push -u origin main

## ͼ��

### [ʹ�ý̳�][Vscode + Github + Picgo �������markdownͼ������ - ֪�� (zhihu.com)](https://zhuanlan.zhihu.com/p/532669042)

��ͼ���е������壬��סctrl+alt+u���ϴ���GitHub�ϡ�
![20241119141106](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119141106.png)
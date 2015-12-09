# FoodMannage
// Logindlg.cpp

#include "stdafx.h"
#include "FoodBevManage.h"
#include "Logindlg.h"
#include "FoodBevManageDlg.h"
#ifdef _DEBUG
#define new DEBUG_NEW
#undef THIS_FILE
static char THIS_FILE[] = __FILE__;
#endif
CLogindlg::CLogindlg(CWnd* pParent /*=NULL*/)
	: CDialog(CLogindlg::IDD, pParent)
{
	//{{AFX_DATA_INIT(CLogindlg)
	m_Username = _T("");
	m_Userpasswd = _T("");
	//}}AFX_DATA_INIT
	i = 0;
}
void CLogindlg::DoDataExchange(CDataExchange* pDX)
{
	CDialog::DoDataExchange(pDX);
	//{{AFX_DATA_MAP(CLogindlg)
	DDX_Text(pDX, IDC_EDIT1, m_Username);
	DDX_Text(pDX, IDC_EDIT2, m_Userpasswd);
	//}}AFX_DATA_MAP
}
BEGIN_MESSAGE_MAP(CLogindlg, CDialog)
END_MESSAGE_MAP()

void CLogindlg::OnOK() 
{
	CMyApp *theApp=(CMyApp*)AfxGetApp();
	UpdateData(TRUE);   //得到用户名与密码
	if(!m_Username.IsEmpty()||!m_Userpasswd.IsEmpty())//判断用户名与密码是否为空
	{
		CString sql="SELECT * FROM LoginInfo WHERE Uname='"+m_Username+"' and Upasswd='"+m_Userpasswd+"'";//SQL
		try
		{			
			m_pRs.CreateInstance("ADODB.Recordset");
			m_pRs->Open((_variant_t)sql,theApp->m_pCon.GetInterfacePtr(),adOpenDynamic,adLockOptimistic,adCmdText);//打开数据库
			if(m_pRs->adoEOF)//记录集为空
			{
				AfxMessageBox("用户名或密码错误!");	
				m_Username="";
				m_Userpasswd="";
				i++;//次数加1
				UpdateData(false);
				if(i==3)//登录错误次数达到3次
				{
					OnCancel();		//退出对话框
				}
				
			}
			else//记录集不为空
			{
				theApp->name=m_Username;//保存用户名
				theApp->pwd=m_Userpasswd;//保存密码
				CDialog::OnOK();//销毁对话框
				return;
			}
		}
		catch(_com_error e)
		{
			CString temp;
			temp.Format("连接数据库错误信息:%s",e.ErrorMessage());
			MessageBox(temp);
			return;
		}
	}
	else
	{
		MessageBox("用户名密码不能为空");
	}
}
BOOL CLogindlg::OnInitDialog() 
{
	CDialog::OnInitDialog();
	SetIcon(LoadIcon(AfxGetInstanceHandle(),MAKEINTRESOURCE(IDI_ICON_login)),TRUE);//设置窗体图标	
	return TRUE;
}

void CLogindlg::OnCancel() 
{
  CDialog::OnCancel();
}

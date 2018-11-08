# observer
1 Active Directoryをインストール・構成する
参照資料：https://www.rem-system.com/post-671/
2 JavaでActiveDirectoryへ接続するための調べ
参照資料：http://nameless-se.blogspot.com/2013/04/ad-java_28.html
/**
 * 
 */
package com.fujifilm.common.util;
import java.util.Hashtable;

import javax.naming.AuthenticationException;
import javax.naming.Context;
import javax.naming.NamingEnumeration;
import javax.naming.NamingException;
import javax.naming.directory.SearchControls;
import javax.naming.directory.SearchResult;
import javax.naming.ldap.Control;
import javax.naming.ldap.InitialLdapContext;
import javax.naming.ldap.LdapContext;

import org.apache.log4j.Logger;

import com.fujifilm.common.config.CISASystemConfig;

/**
 * @author kigak-ka
 *
 */
public class LDAPHelper {

	private static Logger logger = Logger.getLogger(CompressManager.class.getName());
	// AD接続先URL
	private final String URL = "ldap://WIN-AHK2V41L7MV.cimsa.com/";
	// LDAPベースDN
	private final String BASEDN = "DC=cimsa,DC=com";
	// Contextファクトリ
	private final String FACTORY = "com.sun.jndi.ldap.LdapCtxFactory";

	private LdapContext ctx = null;

	private final Control[] connCtls = null;

	private void LDAP_connect() {
		Hashtable<String, String> env = new Hashtable<String, String>();
		env.put(Context.INITIAL_CONTEXT_FACTORY, FACTORY);
		env.put(Context.PROVIDER_URL, URL + BASEDN);
		 // セキュリティレベル
		env.put(Context.SECURITY_AUTHENTICATION, "simple");
		// LDAPバインドユーザーDN
		String root = "cn=cimsatest,cn=Users," + BASEDN;
		env.put(Context.SECURITY_PRINCIPAL, root );
		// パスワード
		env.put(Context.SECURITY_CREDENTIALS, "5%cimsa");

		try {
			ctx = new InitialLdapContext(env, connCtls);
			logger.info("LDAP接続成功 :" + root);
			System.out.println("接続成功");
		} catch (NamingException e) {
			logger.info("LDAP接続失敗 :" + root);
			System.out.println("接続失敗");
		}
	}

	private String getUserDN(String uid) {
		String userDN = "";
		LDAP_connect();
		if(!(ctx == null)) {
			try {
				SearchControls constraints = new SearchControls();
				constraints.setSearchScope(SearchControls.SUBTREE_SCOPE);

				NamingEnumeration<SearchResult> en = ctx.search("", "uid=" + uid, constraints);

				if (en == null || !en.hasMoreElements()) {
					logger.info("ユーザが存在しません. ユーザ名:" + uid);
				}
				// maybe more than one element
				while (en != null && en.hasMoreElements()) {
					Object obj = en.nextElement();
					if (obj instanceof SearchResult) {
						SearchResult si = (SearchResult) obj;
						userDN += si.getName();
						userDN += "," + BASEDN;
					} else {
						System.out.println(obj);
					}
				}
			} catch (Exception e) {
				logger.info("ユーザ取得失敗 :" + userDN);
			}
		}
		return userDN;
	}
	/*
	 * @param String UID バインドユーザー名
	 * @param String PassWord LDAPバインドユーザーパスワード
	 * @param String OU 所属組織
	 * @param String BaseDN　ベースDN
	 * @param String LDAPURL 接続先サーバ名（LB）
	 * @return boolean valide(true:有効；false：無効)
	 * @throws Exception
	 */
	public boolean authenricate(String UID, String PassWord) {
		boolean valide = false;
		String userDN = getUserDN(UID);
		if(!"".equals(userDN)) {
			try {
				ctx.addToEnvironment(Context.SECURITY_PRINCIPAL, userDN);
				ctx.addToEnvironment(Context.SECURITY_CREDENTIALS, PassWord);
				ctx.reconnect(connCtls);
				logger.info("パスワードの検証に成功しました :" + userDN);
				valide = true;
			} catch (AuthenticationException e) {
				logger.info("パスワードの検証に失敗しました :" + userDN);
				System.out.println(e.toString());
				valide = false;
			} catch (NamingException e) {
				logger.info("パスワードの検証に失敗しました :" + userDN);
				valide = false;
			}
			try {
				ctx.close();
			} catch (NamingException e) {
				logger.info("ctx close失败 :" + userDN);
				System.out.println(userDN + "close失败");
				//e.printStackTrace();
				valide = false;
			}
		}
		return valide;
	}	
}

#9.2操作系统的ContentProvider
实际上，Android系统本身提供了大量的ContenProvider，例如联系人信息，系统的多媒体信息等，开发者自己开发的Android应用也可通过ContentResolver来调用系统ContentProvider提供的query（），update（）和delete（）方法，这样开发者即可通过这些ContentProvider来获取Android内部的数据了。
###9.2.1使用ContentProvider管理联系人
Android系统提供了Contacts应用程序来管理联系人，而且Andriod系统还为联系人管理提供了Contentprovider，这就允许其他应用程序以ContentResolver来管理联系人数据。

Android系统用于管理联系人的ContentProvider的几个Uri。

- ContactsContract.Contacts.CONTENT_URI:管理联系人的Uri
- ContactsContract.CommonDataKinds.Phone.CONTENT_URI:管理联系人的电话的Uri。
- ContactsContract.CommonDataKinds.Email.CONTENT_URI:管理联系人的E-mail的Uri。

下面实例程序中包含两个按钮，其中一个按钮用于查询系统的联系人数据，该按钮所绑定的事件监听器代码如下。

程序清单：
	
	search.setOnClicklistener(new OnClicklistener()
	{
	   @Override
	   public void onClick(View source)
	   {
	      //定义两个List来封装系统的联系人信息，指定联系人的电话号码，E-mail等详情
	      final ArrayList<String> names = new ArrayList<>();
	      final ArrayList<ArrayList<String>> details = new ArrayList<>();
	      //使用ContentResolver查找联系人数据
	      Cursor cursor = getContentResolver().query(ContactsContract.Contacts.CONTENT_URI,null,null,null,null);
	      //遍历查询结果，获取系统中所有联系人
	      while (cursor.moveToNext())
	      {
	         //获取联系人ID
	         String contactId = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts._ID));
	         //获取联系人的名字
	         String name = cursor.getString(cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
	         name.add(name);
            //使用ContentResolver查找联系人的电话号码
	         Cursor phones = getContentResolver().query(ContactsContract.CommonDataKinds.phone.CONTENT_URI,null,ContactsContract.CommonDataKinds.phone.CONTACT_ID + " = " + contactID ,null,null);
	         ArrayList<String> datail = new ArrayList<>();
	         //遍历查询结果，获取该联系人的多个电话号码
	         while (phones.moveToNext())
	         {
	            //获取查询结果中电话号码列中的数据
	            String phoneNumber = phone.getString(phones.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
	            detail.add("电话号码: " + phoneNumber);
	         }
	         phones.close();
	         //使用ContentResolver查找联系人的E-mail地址
	         Cursor emails = getContentResolver().query(ContactsContract.CommonDataKinds.Email.CONTENT_URI,null,ContactsContract.CommonDataKinds.Email.CONTACT_ID + " = " +contactId,null,null);
	         //遍历查询结果，获取该联系人的多个E-mail地址
	         while (email.moveToNext())
	         {
	            //获取查询结果中E-mail地址列中的数据
	            String emailAddress = emails.getString(emails.getColimnIndex(ContactsContract.CommonDataKinds.Email.DATA));
	            detail.add("邮件地址： " + emailAddress);
	         }
	         emails.close();
	         details.add(detail);
	      }
	      cursor.close();
	      //加载result.xml界面布局代表的视图
	      View resultDialog = getLayoutInflater().inflate(R.layout.result.null);
	      //获取resultDialog中ID为list的ExpandableListView
	      ExpandableListView list = (ExpandableListView) resultDialog.findViewById(R.id.list);
	      //创建一个ExpandableListAdapter对象
	      ExpandableListAdapter adapter = new BaseExpandableListAdapter()
	      {
	         //获取指定组位置、指定自列表项处的子列表数据
	         @Override
	         public Object getChild(int groupPosition,int childPosition)
	         {
	            return detail.get(groupPosition).get(childPosition);
	         }
	         @Override
	         public long getChildId(int groupPosition,int childPosition)
	         {
	            return childPosition;
	         }
	         @Override
	         public int getChildCount(int groupPosition)
	         {
	            return details.get(groupPosition).size();
	         }
	         private TextView getTextView()
	         {
	            AbsListView.LayoutParams lp = new AbsListView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT,64);
	            TextView textView = new TextView(MainActivity.this);
	            textView.setLayoutParams(lp);
	            textView.setGravity(Gravity.CENTER_VERTICAL|Gravity.LEFT);
	            textView.setPadding(36,0,0,0);
	            textView.setTextSize(20);
	            return textView;
	         }
	         //该方法决定每个子选项的外观
	         @Override
	         public View getChildView(int groupPosition,int childPosition,boolean isLastChild,View convertView,ViewGroup parent)
	         {
	            TextView textView=getTextView();
	            textView.setText(getChild(groupPosition,childPosition).toString());
	            return textView;
	         }
	         //获取指定组位置处的组数据
	         @Override
	         public Object getGroup(int groupPosition)
	         {
	            return names.get(groupPosition);
	         }
	         @Override
	         public int getGroupCount()
	         {
	            return names.size();
	         }
	         @Override
	         public long getGroupId(int groupPosition)
	         {
	            return groupPosition;
	         }
	         //该方法决定每个组选项的外观
	         @Override
	         public View getGroupView(int groupPosition,boolean isExpanded,View convertView,ViewGroup parent)
	         {
	            TextView textView = getTextView();
	            textView.setText(getGroup(groupPosition).toString());
	            return textView;
	         }
	         @Override
	         public boolean isChildSelectable(int groupPosition,int childPosition)
	         {
	            return true;
	         }
	         @Override
	         public boolean hasStableIds()
	         {
	            return true;
	         } 
	      };
	   //为ExpandableListView设置Adapter对象
	   list.setAdapter(adapter);
	   //使用对话框来显示查询结果
	   new AlertDialog.Builder(MainActivity.this).setView(resultDialog).setPositiveButton("确定",null).show();
	}
	});
上面程序中使用ContentResolver向ContactsContract.Contacts.CONTENT_URI查询数据，将可以把系统中所有联系人信息查询出来；使用ContentResolver向ContactsContract.CommonDataKinds.Phone.CONTENT_URI查询数据，用于查询指定联系人的电话信息；使用ContentResolver向ContactsContract.CommonDataKinds.Email.CONTENT_URI查询数据，用于查询指定联系人的E-mail信息。

上面的程序通过ContentResolver将联系人信息、电话信息、E-mail信息查询出来之后，使用了一个ExpandableListView来显示所有联系人信息。

###9.2.2使用ContentProvider管理多媒体内容
Android提供了Camera程序来支持拍照、拍摄视频，用户拍摄的照片、视频都将存放在固定的位置。有些时候，其他应用程序可能需要直接访问Camera所拍摄的照片、视频等，为了处理这种需求，Android同样为这些多媒体内容提供了ContentProvider。

Android为多媒体提供的ContentProvider的Uri如下：

- MediaStore.Audio.Media.EXTERNAL_CONTENT_URI:存储在外部存储器（SD卡）上的音频文件内容的ContentProvider的Uri。
- MediaStore.Audio.Media.INTERNAL_CONTENT_URI:存储在手机内部存储器上的音频文件内容的ContentProvider的Uri。
- MediaStore.Image.Media.EXTERNAL_CONTENT_URI:存储在外部存储器（SD卡）上的图片文件内容的ContentProvider的Uri。
- MediaStore.Image.Media.INTERNAL_CONTENT_URI:存储在手机内部存储器上的图片文件内容的ContentProvider的Uri。
- MediaStore.Video.Media.EXTERNAL_CONTENT_URI:存储在外部存储器（SD卡）上的视频文件内容的ContentProvider的Uri。
- MediaStore.Video.Media.INTERNAL_CONTENT_URI:存储在手机内部存储器上的视频文件内容的ContentProvider的Uri。

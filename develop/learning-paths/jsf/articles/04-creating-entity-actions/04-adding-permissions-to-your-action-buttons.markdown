# Adding Permissions to Your Action Buttons [](id=adding-permissions-to-your-action-buttons)

Recall from the previous learning path when you created wrapper classes to hold
permissions methods. You also updated your managed beans to call those wrapped
entities. By doing this, the guestbook and entry entities you use in your
managed beans are wrapped with the permissions you create in the wrapper
classes. 

At the moment, you have bare wrapper classes with no permissions methods. You'll
change this predicament by adding three properties and corresponding methods
that can be called from your `master` view that check if the user has the
appropriate permissions to access each of your action buttons. You'll also
provide an additional property and method that generates the permissions URL for
managing an entity's permissions. It's time to expand your Guestbook portlet's
permissioning! 

1. In the `com.liferay.docs.guestbook.wrappers.Guestbook` class, add the
   following properties: 

        private Boolean deleteable;
        private Boolean permissible;
        private Boolean updateable;

    These three properties will be used in your permissions methods, and called
    from the `master` view. 

2. Add the following permissions methods directly below your constructor method:

        public Boolean getDeleteable() {

            if (deleteable == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                deleteable = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getGuestbookId(), ActionKeys.DELETE);
            }

            return deleteable;
        }

        public Boolean getPermissible() {

            if (permissible == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                permissible = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getGuestbookId(), ActionKeys.PERMISSIONS);
            }

            return permissible;
        }

        public Boolean getUpdateable() {

            if (updateable == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                updateable = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getGuestbookId(), ActionKeys.UPDATE);
            }

            return updateable;
        }

    These three methods' returned properties correspond to the three permissions
    that can be granted to users. For example, the `getDeleteable()` method
    checks if the current user has the appropriate permissions to use the Delete
    action button for the guestbook entity. The method uses the
    [`LiferayFacesContext`](https://github.com/liferay/liferay-faces/blob/master/portal/src/main/java/com/liferay/faces/portal/context/LiferayFacesContext.java)
    to grab the
    [`ThemeDisplay`](https://github.com/liferay/liferay-portal/blob/master/portal-service/src/com/liferay/portal/theme/ThemeDisplay.java),
    and then checks if the user has the appropriate permissions to access the
    button by calling Liferay's
    [`PermissionChecker`](https://github.com/liferay/liferay-portal/blob/master/portal-service/src/com/liferay/portal/security/permission/PermissionChecker.java).
    The `PermissionChecker` scans the Guestbook's model resource to see if the
    current user holds the `DELETE` action key. If the user's role supports the
    action key, the Delete button is visible and usable; if not, the button is
    invisible to the user. 

    The other two methods work in a very similar way. The `getPermissible()`
    method checks for the Permissions button's permissions, and the
    `getUpdateable()` method checks for the Edit button's permissions. 

3. Repeat step 1 for the `com.liferay.docs.guestbook.wrappers.Entry` class. The
   properties are identical for both wrapper classes. 

4. In the `Entry` wrapper class, add the following permissions methods directly
   below your constructor method: 

        public Boolean getDeleteable() {

            if (deleteable == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                deleteable = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getEntryId(), ActionKeys.DELETE);
            }

            return deleteable;
        }

        public Boolean getPermissible() {

            if (permissible == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                permissible = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getEntryId(), ActionKeys.PERMISSIONS);
            }

            return permissible;
        }

        public Boolean getUpdateable() {

            if (updateable == null) {
                LiferayFacesContext liferayFacesContext = LiferayFacesContext.getInstance();
                long scopeGroupId = liferayFacesContext.getScopeGroupId();
                updateable = liferayFacesContext.getThemeDisplay().getPermissionChecker().hasPermission(scopeGroupId,
                    MODEL, getEntryId(), ActionKeys.UPDATE);
            }

            return updateable;
        }

4. Now that your wrapper classes hold the necessary permission check methods and
   properties, you'll check for them in your `master` view. First, for each of
   your guestbook buttons you just created, surround each individual
   `<h:commandButton>...</h:commandButton>` tag with the
   `<h:panelGroup>...</h:panelGroup>` tag. Each panel group tag should contain
   the `rendered` element, specifying the appropriate permission to check for
   each action button. Below is how you guestbook buttons should look after
   you're finished: 

        <div id="guestbook_buttons" style="display: inline-block">
            <h:panelGroup rendered="#{guestbookBacking.selectedGuestbook.updateable}">
                <h:commandButton action="#{guestbookBacking.edit(guestbookBacking.selectedGuestbook)}" styleClass="btn btn-default" value=" #{i18n['edit']} ">
                    <f:ajax render="@all" />
                </h:commandButton>
            </h:panelGroup>
            <h:panelGroup rendered="#{guestbookBacking.selectedGuestbook.deleteable}">
                <h:commandButton action="#{guestbookBacking.delete(guestbookBacking.selectedGuestbook)}" styleClass="btn btn-default" value=" #{i18n['delete']} "
                    onclick="if (! confirm('#{i18n['are-you-sure-you-want-to-delete-this']}')) {return false;}" >
                    <f:ajax render="@all" />
                </h:commandButton>
            </h:panelGroup>
            <h:panelGroup rendered="#{guestbookBacking.selectedGuestbook.permissible}">
                <h:outputLink styleClass="btn btn-default" value="#{guestbookBacking.selectedGuestbook.permissionsUrl}">
                    <h:outputText value=" #{i18n['permissions']} " />
                </h:outputLink>
            </h:panelGroup>
        </div>

5. Complete the same process for the entry action buttons. The entries are
   displayed slightly differently than the guestbooks, so you won't need to
   check permissions for the *selected* entry, but rather, just `entry`. Your
   updated entry action buttons code should appear like the code snippet below: 

        <h:column>
            <f:facet name="header"><h:outputText value=" " /></f:facet>
            <h:panelGroup rendered="#{entry.updateable}">
                <h:commandButton action="#{entryBacking.edit(entry)}" styleClass="btn btn-default" value=" #{i18n['edit']} ">
                    <f:ajax render="@all" />
                </h:commandButton>
            </h:panelGroup>
            <h:panelGroup rendered="#{entry.deleteable}">
                <h:commandButton action="#{entryBacking.delete(entry)}" styleClass="btn btn-default" value=" #{i18n['delete']} "
                    onclick="if (! confirm('#{i18n['are-you-sure-you-want-to-delete-this']}')) {return false;}" >
                    <f:ajax render="@all" />
                </h:commandButton>
            </h:panelGroup>
            <h:panelGroup rendered="#{entry.permissible}">
                <h:outputLink styleClass="btn btn-default" value="#{entry.permissionsUrl}">
                    <h:outputText value=" #{i18n['permissions']} " />
                </h:outputLink>
            </h:panelGroup>
        </h:column>

Congratulations! You've implemented custom actions for your entities. Users can
now manage an entity's full life cycle: adding, editing, modifying the
permissions of, and deleting an entity. 

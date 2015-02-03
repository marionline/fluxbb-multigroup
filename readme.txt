##
##
##        Mod title:  Multigroup
##
##      Mod version:  1.0
##  Works on FluxBB:  1.5.0
##     Release date:  2012-08-17
##      Review date:  2012-08-17
##           Author:  Stefan D
##       Maintainer:  Daris (daris91@gmail.com)
##
##      Description:  This mod adds "multi group functionality", which in plain english means
##                    that you can make users member of more than one group. To prevent weird
##                    memberships you cannot add the administrator or moderator group to the
##                    list of additional groups. If you want a user to be administrator or
##                    moderator you have to set the ordinary user group.
##
##   Repository URL:  http://fluxbb.org/resources/mods/multigroup/
##
##   Affected files:  admin_users.php
##                    index.php
##                    moderate.php
##                    post.php
##                    profile.php
##                    search.php
##                    viewforum.php
##                    viewtopic.php
##                    include/functions.php
##
##       Affects DB:  Yes
##
##       DISCLAIMER:  Please note that "mods" are not officially supported by
##                    FluxBB. Installation of this modification is done at
##                    your own risk. Backup your forum database and any and
##                    all applicable files before proceeding.
##
##


#-------- [ OPEN ] -------

admin_users.php

#-------- [ FIND ] -------

	if ($user_group > -1)
		$conditions[] = 'u.group_id='.$user_group;

#-------- [ REPLACE WITH ] -------

	if ($user_group > -1)
		$conditions[] = 'u.group_id='.$user_group.' OR membergroupids='.$user_group.' OR membergroupids LIKE \'%,'.$user_group.',%\' OR membergroupids LIKE \''.$user_group.',%\' OR membergroupids LIKE \'%,'.$user_group.'\'';


#-------- [ OPEN ] -------

index.php


#-------- [ FIND ] -------

require PUN_ROOT.'header.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

$result = $db->query('SELECT c.id AS cid, c.cat_name, f.id AS fid, f.forum_name, f.forum_desc, f.redirect_url, f.moderators, f.num_topics, f.num_posts, f.last_post, f.last_post_id, f.last_poster FROM '.$db->prefix.'categories AS c INNER JOIN '.$db->prefix.'forums AS f ON c.id=f.cat_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE fp.read_forum IS NULL OR fp.read_forum=1 ORDER BY c.disp_position, c.id, f.disp_position', true) or error('Unable to fetch category/forum list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

$result = $db->query('SELECT c.id AS cid, c.cat_name, f.id AS fid, f.forum_name, f.forum_desc, f.redirect_url, f.moderators, f.num_topics, f.num_posts, f.last_post, f.last_post_id, f.last_poster FROM '.$db->prefix.'categories AS c INNER JOIN '.$db->prefix.'forums AS f ON c.id=f.cat_id '.$mgrp_extra.' ORDER BY c.disp_position, c.id, f.disp_position', true) or error('Unable to fetch category/forum list', __FILE__, __LINE__, $db->error());


#-------- [ OPEN ] -------

moderate.php


#-------- [ FIND ] -------

require PUN_ROOT.'include/common.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

	$result = $db->query('SELECT t.subject, t.num_replies, t.first_post_id, f.id AS forum_id, forum_name FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND f.id='.$fid.' AND t.id='.$tid.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$result = $db->query('SELECT t.subject, t.num_replies, t.first_post_id, f.id AS forum_id, forum_name FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id '.$mgrp_extra.' AND f.id='.$fid.' AND t.id='.$tid.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

$result = $db->query('SELECT f.forum_name, f.redirect_url, f.num_topics, f.sort_by FROM '.$db->prefix.'forums AS f LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND f.id='.$fid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

$result = $db->query('SELECT f.forum_name, f.redirect_url, f.num_topics, f.sort_by FROM '.$db->prefix.'forums AS f '.$mgrp_extra.' AND f.id='.$fid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ OPEN ] -------

post.php


#-------- [ FIND ] -------

require PUN_ROOT.'include/common.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

	$result = $db->query('SELECT f.id, f.forum_name, f.moderators, f.redirect_url, fp.post_replies, fp.post_topics, t.subject, t.closed, s.user_id AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') LEFT JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$pun_user['id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.id='.$tid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$result = $db->query('SELECT f.id, f.forum_name, f.moderators, f.redirect_url, fp.post_replies, fp.post_topics, t.subject, t.closed, s.user_id AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$pun_user['id'].') '.$mgrp_extra.' (fp.read_forum IS NULL OR fp.read_forum=1) AND t.id='.$tid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

$result = $db->query('SELECT f.id, f.forum_name, f.moderators, f.redirect_url, fp.post_replies, fp.post_topics FROM '.$db->prefix.'forums AS f LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND f.id='.$fid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

$result = $db->query('SELECT f.id, f.forum_name, f.moderators, f.redirect_url, fp.post_replies, fp.post_topics FROM '.$db->prefix.'forums AS f '.$mgrp_extra.' AND f.id='.$fid) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());


#-------- [ OPEN ] -------

profile.php


#-------- [ FIND ] -------

	$new_group_id = intval($_POST['group_id']);

#-------- [ INSERT AFTER ] -------

	$new_add_group = (isset($_POST['add_group_id']) ? $_POST['add_group_id'] : null);

	if(is_array($new_add_group)) {
		$db_extra = ', membergroupids=\''.implode(',', $new_add_group).'\'';
	} else {
		$db_extra = ', membergroupids=\'\'';
	}


#-------- [ FIND ] -------

	$db->query('UPDATE '.$db->prefix.'users SET group_id='.$new_group_id.' WHERE id='.$id) or error('Unable to change user group', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$db->query('UPDATE '.$db->prefix.'users SET group_id='.$new_group_id.$db_extra.' WHERE id='.$id) or error('Unable to change user group', __FILE__, __LINE__, $db->error());


#-------- [ FIND ] -------

$result = $db->query('SELECT u.username, u.email, u.title, u.realname, u.url, u.jabber, u.icq, u.msn, u.aim, u.yahoo, u.location, u.signature, u.disp_topics, u.disp_posts, u.email_setting, u.notify_with_post, u.auto_notify, u.show_smilies, u.show_img, u.show_img_sig, u.show_avatars, u.show_sig, u.timezone, u.dst, u.language, u.style, u.num_posts, u.last_post, u.registered, u.registration_ip, u.admin_note, u.date_format, u.time_format, u.last_visit, g.g_id, g.g_user_title, g.g_moderator FROM '.$db->prefix.'users AS u LEFT JOIN '.$db->prefix.'groups AS g ON g.g_id=u.group_id WHERE u.id='.$id) or error('Unable to fetch user info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

$result = $db->query('SELECT u.username, u.email, u.title, u.realname, u.url, u.jabber, u.icq, u.msn, u.aim, u.yahoo, u.location, u.signature, u.disp_topics, u.disp_posts, u.email_setting, u.notify_with_post, u.auto_notify, u.show_smilies, u.show_img, u.show_img_sig, u.show_avatars, u.show_sig, u.timezone, u.dst, u.language, u.style, u.num_posts, u.last_post, u.registered, u.registration_ip, u.admin_note, u.date_format, u.time_format, u.last_visit, u.membergroupids, g.g_id, g.g_user_title, g.g_moderator FROM '.$db->prefix.'users AS u LEFT JOIN '.$db->prefix.'groups AS g ON g.g_id=u.group_id WHERE u.id='.$id) or error('Unable to fetch user info', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

<input type="submit" name="update_group_membership" value="<?php echo $lang_profile['Save'] ?>" />

#-------- [ INSERT BEFORE ] -------

<br /><p><?php echo $lang_profile['Additional group membership'] ?></p>
 	                                                         <select multiple id="add_group_id" name="add_group_id[]" size="5">
 	 <?php
 	           $result = $db->query('SELECT g_id, g_title FROM '.$db->prefix.'groups WHERE g_id!='.PUN_GUEST.' ORDER BY g_title') or error('Unable to fetch user group list', __FILE__, __LINE__, $db->error());
 	           $add_groups = explode(",", $user['membergroupids']);
 	           print_r($add_groups);

 	                         while ($cur_group = $db->fetch_assoc($result))
 	                         {
 	                           if($cur_group['g_id'] > PUN_MOD && $cur_group['g_id'] != $user['g_id'] && !($cur_group['g_id'] == $pun_config['o_default_user_group'] && $user['g_id'] == '')) {
 	                               $extra = '';
 	                               if(in_array($cur_group['g_id'], $add_groups))
 	                                 $extra = 'selected="selected"';

 	                   echo "\t\t\t\t\t\t\t\t".'<option value="'.$cur_group['g_id'].'" '.$extra.'>'.pun_htmlspecialchars($cur_group['g_title']).'</option>'."\n";
 	               }
 	                         }
 	 ?>
 	                             </select><br /><br />


#-------- [ OPEN ] -------

search.php


#-------- [ FIND ] -------

require PUN_ROOT.'include/common.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.last_post>'.$pun_user['last_visit'].' AND t.moved_to IS NULL'.(isset($_GET['fid']) ? ' AND t.forum_id='.intval($_GET['fid']) : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t '.$mgrp_extra.' AND t.last_post>'.$pun_user['last_visit'].' AND t.moved_to IS NULL'.(isset($_GET['fid']) ? ' AND t.forum_id='.intval($_GET['fid']) : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.last_post>'.(time() - $interval).' AND t.moved_to IS NULL'.(isset($_GET['fid']) ? ' AND t.forum_id='.intval($_GET['fid']) : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t '.$mgrp_extra.' AND t.last_post>'.(time() - $interval).' AND t.moved_to IS NULL'.(isset($_GET['fid']) ? ' AND t.forum_id='.intval($_GET['fid']) : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'posts AS p ON t.id=p.topic_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND p.poster_id='.$pun_user['id'].' GROUP BY t.id'.($db_type == 'pgsql' ? ', t.last_post' : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'posts AS p ON t.id=p.topic_id '.$mgrp_extra.' AND p.poster_id='.$pun_user['id'].' GROUP BY t.id'.($db_type == 'pgsql' ? ', t.last_post' : '').' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT p.id FROM '.$db->prefix.'posts AS p INNER JOIN '.$db->prefix.'topics AS t ON p.topic_id=t.id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND p.poster_id='.$user_id.' ORDER BY p.posted DESC') or error('Unable to fetch user posts', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT p.id FROM '.$db->prefix.'posts AS p INNER JOIN '.$db->prefix.'topics AS t ON p.topic_id=t.id '.$mgrp_extra.' AND p.poster_id='.$user_id.' ORDER BY p.posted DESC') or error('Unable to fetch user posts', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'posts AS p ON t.first_post_id=p.id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND p.poster_id='.$user_id.' ORDER BY t.last_post DESC') or error('Unable to fetch user topics', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'posts AS p ON t.first_post_id=p.id '.$mgrp_extra.' AND p.poster_id='.$user_id.' ORDER BY t.last_post DESC') or error('Unable to fetch user topics', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$user_id.') LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$user_id.') '.$mgrp_extra.' ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=t.forum_id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.num_replies=0 AND t.moved_to IS NULL ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

				$result = $db->query('SELECT t.id FROM '.$db->prefix.'topics AS t '.$mgrp_extra.' AND t.num_replies=0 AND t.moved_to IS NULL ORDER BY t.last_post DESC') or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

$result = $db->query('SELECT c.id AS cid, c.cat_name, f.id AS fid, f.forum_name, f.redirect_url FROM '.$db->prefix.'categories AS c INNER JOIN '.$db->prefix.'forums AS f ON c.id=f.cat_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND f.redirect_url IS NULL ORDER BY c.disp_position, c.id, f.disp_position', true) or error('Unable to fetch category/forum list', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

$result = $db->query('SELECT c.id AS cid, c.cat_name, f.id AS fid, f.forum_name, f.redirect_url FROM '.$db->prefix.'categories AS c INNER JOIN '.$db->prefix.'forums AS f ON c.id=f.cat_id '.$mgrp_extra.' AND f.redirect_url IS NULL ORDER BY c.disp_position, c.id, f.disp_position', true) or error('Unable to fetch category/forum list', __FILE__, __LINE__, $db->error());

#-------- [ OPEN ] -------

viewforum.php


#-------- [ FIND ] -------

require PUN_ROOT.'lang/'.$pun_user['language'].'/forum.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

	$result = $db->query('SELECT f.forum_name, f.redirect_url, f.moderators, f.num_topics, f.sort_by, fp.post_topics, s.user_id AS is_subscribed FROM '.$db->prefix.'forums AS f LEFT JOIN '.$db->prefix.'forum_subscriptions AS s ON (f.id=s.forum_id AND s.user_id='.$pun_user['id'].') LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND f.id='.$id) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$result = $db->query('SELECT f.forum_name, f.redirect_url, f.moderators, f.num_topics, f.sort_by, fp.post_topics, s.user_id AS is_subscribed FROM '.$db->prefix.'forums AS f LEFT JOIN '.$db->prefix.'forum_subscriptions AS s ON (f.id=s.forum_id AND s.user_id='.$pun_user['id'].') '.$mgrp_extra.' AND f.id='.$id) or error('Unable to fetch forum info', __FILE__, __LINE__, $db->error());

#-------- [ OPEN ] -------

viewtopic.php


#-------- [ FIND ] -------

require PUN_ROOT.'lang/'.$pun_user['language'].'/topic.php';

#-------- [ INSERT AFTER ] -------

// create SQL for multigroup mod
$mgrp_extra = multigrp_getSql($db);


#-------- [ FIND ] -------

	$result = $db->query('SELECT t.subject, t.closed, t.num_replies, t.sticky, t.first_post_id, f.id AS forum_id, f.forum_name, f.moderators, fp.post_replies, s.user_id AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$pun_user['id'].') LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.id='.$id.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$result = $db->query('SELECT t.subject, t.closed, t.num_replies, t.sticky, t.first_post_id, f.id AS forum_id, f.forum_name, f.moderators, fp.post_replies, s.user_id AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'topic_subscriptions AS s ON (t.id=s.topic_id AND s.user_id='.$pun_user['id'].') '.$mgrp_extra.' AND t.id='.$id.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ FIND ] -------

	$result = $db->query('SELECT t.subject, t.closed, t.num_replies, t.sticky, t.first_post_id, f.id AS forum_id, f.forum_name, f.moderators, fp.post_replies, 0 AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id LEFT JOIN '.$db->prefix.'forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id='.$pun_user['g_id'].') WHERE (fp.read_forum IS NULL OR fp.read_forum=1) AND t.id='.$id.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ REPLACE WITH ] -------

	$result = $db->query('SELECT t.subject, t.closed, t.num_replies, t.sticky, t.first_post_id, f.id AS forum_id, f.forum_name, f.moderators, fp.post_replies, 0 AS is_subscribed FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id '.$mgrp_extra.' AND t.id='.$id.' AND t.moved_to IS NULL') or error('Unable to fetch topic info', __FILE__, __LINE__, $db->error());

#-------- [ OPEN ] -------

include/functions.php


#-------- [ FIND ] -------

	echo '</pre>';
	exit;
}

#-------- [ INSERT AFTER ] -------

// creates sql-query for multigroup mod
function multigrp_getSql($db) {
	global $pun_user;

	$retJoin = "LEFT JOIN ".$db->prefix."forum_perms AS fp ON (fp.forum_id=f.id AND fp.group_id=".$pun_user['g_id'].")";
	$retWhere = " (fp.read_forum IS NULL OR fp.read_forum=1)";

	$mgrps = explode(',', $pun_user["membergroupids"]);
	$count = 1;
	foreach($mgrps as $mgrp) {
		if((int)$mgrp != 0) {
			$retJoin  .= " LEFT JOIN ".$db->prefix."forum_perms AS fp".$count." ON (fp".$count.".forum_id=f.id AND fp".$count.".group_id=".$mgrp.")";
			$retWhere .= " OR (fp".$count.".read_forum IS NULL OR fp".$count.".read_forum=1)";
			$count++;
		}
	}

	return $retJoin." WHERE (".$retWhere.") ";
}


#-------- [ OPEN ] -------

lang/English/profile.php


#-------- [ FIND ] -------

// Administration stuff

#-------- [ INSERT AFTER ] -------

'Additional group membership' => 'Additional user groups (hold down CTRL to select more than one group)',

#-------- [ END ] -------
